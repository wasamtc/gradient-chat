# 1.在tokenizermanager中对lora的处理

在类TokenizerManager中的方法generate_request，会以读锁的方式检查是否启用lora，然后根据lora_path获取lora适配器的id（acquire会增加计数）并赋给obj。然后把请求发送给scheduler，然后进入_wait_one_response函数，在这个函数里面等待请求完成并release掉使用lora适配器，计数减1

同时tokenizermanager也负责lora的动态加载与卸载，因为sglang中可能同时存在多个lora适配器，但不是每个适配器都在使用中或内存中，需要对适配器进行动态的记载和卸载。

加载适配器的入口在类TokenizerCommunicatorMixin的方法load_lora_adapter中，检查是否开启lora和当前lora最大值是否大于lora数量，然后创建一个loraref，通知后端加载适配器，将loraref注册到loraregister中

详细说下后端加载适配器这个过程：在tokenizermanager中将的适配器包装为一个LoadLoRAAdapterReqInput，然后通过commuiter发送给scheduler，scheduler路由到它的load_lora_adapter函数，继续到tpwork的load_lora_adapter，继续到model_runner的load_lora_adapter

## 1.1LoRAConfig

对于lora微调，主要公式如下
$$
\Delta W = (B A) \times \frac{\alpha}{r}
$$
主要有三个参数需要确定：α，r，target_modules(应用到哪些模块)

在使用sglang时，我们可以通过--lora-paths参数指定一个或多个LoRA适配器路径（支持Hugging Face模型ID或本地路径），这些lora适配器的关键配置以loraconfig形式存在

## 1.2 LoRARef

可能存在相同的lora_path，为了区分不同的lora适配器，使用loraref类为每一个类绑定一个唯一的id，`--lora-paths` 参数会被转换为 `LoRARef` 对象列表

## 1.3 LoRARegistry

LoRARegistry是一个中央注册表，负责管理所有的lora适配器，所谓的管理，是通过一个lora适配器的name到id的映射表和一个id到引用计数的映射表完成的，这个类在TokenizerManager初始化的时候作为其一个成员初始化

## 1.4 todo:loramanager