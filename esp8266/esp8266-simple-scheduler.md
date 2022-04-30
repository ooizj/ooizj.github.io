# esp8266 simple scheduler

### esp8266 实现一个简单的调度器

Scheduler.h

```cpp
#ifndef __SCHEDULER
#define __SCHEDULER

#include <functional>
#include <vector>

using namespace std::placeholders;

class CallbackInfo {
    public:
        std::function<void()> fun;
        long interval;
        long last_time = 0;

        CallbackInfo(std::function<void()> fun, long interval): fun(fun), interval(interval){}
};

class Scheduler{
    public:
        vector<CallbackInfo*> funs;
        int schedule(std::function<void()> fun, long interval);
        void loop();

        static Scheduler& instance(){
            static Scheduler INSTANCE;
            return INSTANCE;
        }

};


#endif;



```

Scheduler.cpp

```cpp
#include "Scheduler.h"
#include <Arduino.h>

int Scheduler::schedule(std::function<void()> fun, long interval){
    funs.push_back(new CallbackInfo(fun, interval));
    return 0;
}

void Scheduler::loop(){
    for(int i = 0; i < funs.size(); i++){
        CallbackInfo* cb = funs[i];
        long current_time = millis();
        if( current_time - cb->last_time > cb->interval ){
            cb->fun();
            cb->last_time = millis();
        }
    }
}

```

使用示例

main.cpp

```cpp
#include <Arduino.h>
#include "Scheduler.h"

// 被调度的测试类
class C1{
public:
    // 被调度的测试方法
    virtual void m1();
};
void C1::m1(){
  Serial.println("hello scheduler!");
}

void setup(void)
{
  Serial.begin(9600);
  
  // 测试使用调度
  C1* c1 = new C1();
  auto callback = std::bind(&C1::m1, c1);
  Scheduler::instance().schedule(callback, 1000); // 1秒钟执行一次
}

void loop(void)
{
  Scheduler::instance().loop();
  delay(1);
}

```

控制台打印

```bash
hello scheduler!
hello scheduler!
hello scheduler!
```
