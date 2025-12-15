# 1. radix cache设计

radix cache沿用sglang中的数据结构为radix tree，tree的节点为tree node，node中存储key以及kv cache存储位置对应的token

# 2. 在parallax中的match prefix位置

在`/src/parallax/server/executor/base_executor.py`的`prepare_batch_inputs`函数中，在使用`_prepare_prefill_batch`函数前根据`match_prefix`的结果对请求进行排序

对于`sglang_executor`，在形成ScheduleBatch时传入排序好的req（可以先不做排序，只对req中的indices赋值）

对于`mlx_executor`，在`_prepare_prefill_batch`中添加前缀匹配逻辑（可以先不做排序），对于未匹配的再分配kv cache。对于匹配的，需要考虑radix cache如何对应page cache

# 3. 在parallax中的cache insert位置

在process_batch后更新radix cache

# 4. 其他需要考虑的

radix cache的key是一页page的tokens，需要考虑page_size的大小

radix cache中存储的kv cache对应的token