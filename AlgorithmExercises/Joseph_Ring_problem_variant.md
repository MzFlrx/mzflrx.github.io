# 约瑟夫环问题变体
不再形成环形队列，如果走到左边，则反向继续，如果走到右边则反向继续。
```cpp
#include <bits/stdc++.h>

using namespace std;

int main(){
    int n,m;
    cin>>n>>m;

    list<int> array(n, 0);
    int i=1;
    for(auto it = array.begin(); it!=array.end(); it++){
        *it=i++;
    }
    int index = 1;

    auto it = array.begin();
    int sign = 1;

    while(array.size()!=1){

        while(index != m && array.size() != 1){
            index++;
            if(it == --array.end()){
                it = array.end();
                it--;
                it--;
                sign = -1;
            }else if(it == array.begin()){
                it++;
                sign = 1;
            }else{
                if(sign == 1){
                    it++;
                }else{
                    it--;
                }
            }
        }

        if(it == array.begin() && array.size()!=1){
            auto tmp = it;
            it++;
            sign = 1;
            index = 1;
            array.erase(tmp);
        }
        if(it == --array.end() && array.size()!=1){
            auto tmp = it;
            it--;
            sign = -1;
            index = 1;
            array.erase(tmp);
        }
        if(index == m && array.size()!=1){
            auto tmp = it;
            if(sign == 1){
                it++;
                array.erase(tmp);
                if(it == array.end()){
                    it--;
                    sign =-1;
                }
            }else{
                if(it == array.begin()){
                    it++;
                    array.erase(tmp);
                }else{
                    it--;
                    array.erase(tmp);
                }
            }
            index = 1;
        }
    }

    cout<<*array.begin()<<endl;

    return 0;
}
```
