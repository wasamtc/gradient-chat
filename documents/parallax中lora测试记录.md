1.只使用enable-lora参数 ✅

sglang中报错，parallax报相同错误

2.使用enable-lora+max-lora-rank参数 ✅

报相同错误

3.使用enable-lora+max-lora-rank+lora-target-modules ✅

可以正常运行

4.使用lora-paths ✅

使用`codelion/Qwen3-0.6B-accuracy-recovery-loraA`作为lora适配器

可以正常运行