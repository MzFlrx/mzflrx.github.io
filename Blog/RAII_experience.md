# RAII好的例子
之前一直听说RAII，没看到用过。在llvm中找到一个很好的例子。

这个例子使用RAII机制，保存一个变量的初始状态。在函数退出后，自动重置为原始状态。（如果有问题，那就是我理解的有问题:smile:）

## LLVM中SaveAndRestore类
```cpp
//===-- SaveAndRestore.h - Utility  -------------------------------*- C++ -*-=//
//
// Part of the LLVM Project, under the Apache License v2.0 with LLVM Exceptions.
// See https://llvm.org/LICENSE.txt for license information.
// SPDX-License-Identifier: Apache-2.0 WITH LLVM-exception
//
//===----------------------------------------------------------------------===//
///
/// \file
/// This file provides utility classes that use RAII to save and restore
/// values.
///
//===----------------------------------------------------------------------===//

#ifndef LLVM_SUPPORT_SAVEANDRESTORE_H
#define LLVM_SUPPORT_SAVEANDRESTORE_H

namespace llvm {

/// A utility class that uses RAII to save and restore the value of a variable.
template <typename T> struct SaveAndRestore {
  SaveAndRestore(T &X) : X(X), OldValue(X) {}
  SaveAndRestore(T &X, const T &NewValue) : X(X), OldValue(X) {
    X = NewValue;
  }
  ~SaveAndRestore() { X = OldValue; }
  T get() { return OldValue; }

private:
  T &X;
  T OldValue;
};

} // namespace llvm

#endif


```

## 使用到的函数
```cpp
bool TargetPassConfig::addCoreISelPasses() {
  // Enable FastISel with -fast-isel, but allow that to be overridden.
  TM->setO0WantsFastISel(EnableFastISelOption != cl::BOU_FALSE);

  // Determine an instruction selector.
  enum class SelectorType { SelectionDAG, FastISel, GlobalISel };
  SelectorType Selector;

  if (EnableFastISelOption == cl::BOU_TRUE)
    Selector = SelectorType::FastISel;
  else if (EnableGlobalISelOption == cl::BOU_TRUE ||
           (TM->Options.EnableGlobalISel &&
            EnableGlobalISelOption != cl::BOU_FALSE))
    Selector = SelectorType::GlobalISel;
  else if (TM->getOptLevel() == CodeGenOpt::None && TM->getO0WantsFastISel())
    Selector = SelectorType::FastISel;
  else
    Selector = SelectorType::SelectionDAG;

  // Set consistently TM->Options.EnableFastISel and EnableGlobalISel.
  if (Selector == SelectorType::FastISel) {
    TM->setFastISel(true);
    TM->setGlobalISel(false);
  } else if (Selector == SelectorType::GlobalISel) {
    TM->setFastISel(false);
    TM->setGlobalISel(true);
  }

  // FIXME: Injecting into the DAGISel pipeline seems to cause issues with
  //        analyses needing to be re-run. This can result in being unable to
  //        schedule passes (particularly with 'Function Alias Analysis
  //        Results'). It's not entirely clear why but AFAICT this seems to be
  //        due to one FunctionPassManager not being able to use analyses from a
  //        previous one. As we're injecting a ModulePass we break the usual
  //        pass manager into two. GlobalISel with the fallback path disabled
  //        and -run-pass seem to be unaffected. The majority of GlobalISel
  //        testing uses -run-pass so this probably isn't too bad.
  SaveAndRestore<bool> SavedDebugifyIsSafe(DebugifyIsSafe);
  if (Selector != SelectorType::GlobalISel || !isGlobalISelAbortEnabled())
    DebugifyIsSafe = false;

  // Add instruction selector passes.
  if (Selector == SelectorType::GlobalISel) {
    SaveAndRestore<bool> SavedAddingMachinePasses(AddingMachinePasses, true);
    if (addIRTranslator())
      return true;

    addPreLegalizeMachineIR();

    if (addLegalizeMachineIR())
      return true;

    // Before running the register bank selector, ask the target if it
    // wants to run some passes.
    addPreRegBankSelect();

    if (addRegBankSelect())
      return true;

    addPreGlobalInstructionSelect();

    if (addGlobalInstructionSelect())
      return true;

    // Pass to reset the MachineFunction if the ISel failed.
    addPass(createResetMachineFunctionPass(
        reportDiagnosticWhenGlobalISelFallback(), isGlobalISelAbortEnabled()));

    // Provide a fallback path when we do not want to abort on
    // not-yet-supported input.
    if (!isGlobalISelAbortEnabled() && addInstSelector())
      return true;

  } else if (addInstSelector())
    return true;

  // Expand pseudo-instructions emitted by ISel. Don't run the verifier before
  // FinalizeISel.
  addPass(&FinalizeISelID);

  // Print the instruction selected machine code...
  printAndVerify("After Instruction Selection");

  return false;
}

```