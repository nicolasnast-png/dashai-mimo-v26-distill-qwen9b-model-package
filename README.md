# dashai-mimo-v26-distill-qwen9b-model-package

A [DashAI](https://github.com/DashAISoftware/DashAI) plugin that adds **MiMo-V2.6 Distill Qwen 9B** as a text generation model: Xiaomi's MiMo-V2.6-Distill-Qwen-9B, a Qwen3.5 9B model supervised fine-tuned by Xiaomi MiMo on 77.4B tokens of MiMo-generated data (code, agents, general office tasks and vision), in the GGUF quantizations published by bartowski. It runs locally with llama.cpp and works in DashAI's generative chat and **RAG** pipelines.

Xiaomi reports gains over Qwen3.5-9B on agentic and office benchmarks, e.g. OfficeQA 9.0 -> 19.5 (grounded reasoning over large document collections) and SWE Pro 32.0 -> 44.6. It publishes no long-context benchmark: evaluate it on your own documents.

This is a community plugin. It is not affiliated with the original model authors.

## What the plugin adds

- **One component per quantization.** Each component is a `GGUFTextGenerationModel` that downloads only its own GGUF file, like the built-in GGUF models.

| Component | Quantization | Download |
| --- | --- | --- |
| `MiMoV26DistillQwen9B_Q3_K_S` | Q3_K_S | 4.26 GB |
| `MiMoV26DistillQwen9B_Q3_K_M` | Q3_K_M | 4.48 GB |
| `MiMoV26DistillQwen9B_IQ4_XS` | IQ4_XS | 5.23 GB |
| `MiMoV26DistillQwen9B_Q4_K_S` | Q4_K_S | 5.48 GB |
| `MiMoV26DistillQwen9B_Q4_K_M` | Q4_K_M | 5.84 GB |
| `MiMoV26DistillQwen9B_Q5_K_S` | Q5_K_S | 6.50 GB |
| `MiMoV26DistillQwen9B_Q5_K_M` | Q5_K_M | 6.88 GB |
| `MiMoV26DistillQwen9B_Q6_K` | Q6_K | 7.79 GB |
| `MiMoV26DistillQwen9B_Q8_0` | Q8_0 (8-bit) | 9.55 GB |
| `MiMoV26DistillQwen9B_BF16` | BF16 (unquantized) | 17.92 GB |

- **Standard GGUF parameters:** `max_tokens`, `temperature`, `frequency_penalty`, `context_window` and `device`. The model supports up to 262,144 tokens of context.
- **Partial GPU offload (`gpu_layers`):** when `device` is a GPU, `gpu_layers` sets how many layers go to the GPU. The default, `-1`, offloads all of them; the rest run on CPU. Use it when the model does not fit in GPU memory: on a 6 GB GPU, a ~5 GB quantization fits with about 30 layers. The setting is ignored when `device` is `CPU`. GPU use requires a CUDA build of `llama-cpp-python`.
- **Thinking mode disabled:** this is a reasoning model, and by default it writes a `<think>…</think>` block before answering. The plugin renders the model's own chat template with `enable_thinking=False`, which makes the model answer directly. It also strips any leftover `<think>` block.

## Requirements

- **DashAI:** a version that provides `GGUFTextGenerationModel` (0.11.0 or later; tested on the `develop` branch).
- **llama.cpp:** must support the `qwen35` architecture. `llama-cpp-python` 0.3.34, the version shipped with DashAI `develop`, supports it.
- **Memory:** disk space equal to the download size of the chosen quantization. For CPU inference, free RAM of at least that size plus about 2 GB, more for long contexts.

## Install

**From DashAI:** open the plugin store, search for `dashai-mimo-v26-distill-qwen9b-model-package` and install it.

**Manually:** install into DashAI's plugin directory, then restart DashAI:

```bash
pip install --no-deps --target ~/.DashAI/plugins/py3.12 dashai-mimo-v26-distill-qwen9b-model-package
```

Then download the weights of the quantization you want from its model card in DashAI, or with the API:

```bash
curl -X POST http://localhost:8000/api/v1/component/MiMoV26DistillQwen9B_IQ4_XS/download
```

## Use in a RAG session

Set the generation model of a RAG session to one of the components above. Example API payload:

```json
{"generation_model": {"component": "MiMoV26DistillQwen9B_IQ4_XS",
  "params": {"max_tokens": 600, "temperature": 0.0, "frequency_penalty": 0.1,
             "context_window": 10000, "device": "CPU"}}}
```

## Español

Plugin comunitario para DashAI que agrega **MiMo-V2.6 Distill Qwen 9B**: MiMo-V2.6-Distill-Qwen-9B de Xiaomi, un Qwen3.5 9B ajustado (SFT) por Xiaomi MiMo con 77.400 millones de tokens de datos generados por MiMo (código, agentes, tareas generales de oficina y visión), en las cuantizaciones GGUF publicadas por bartowski.

- **Cuantizaciones:** cada cuantización aparece como un modelo aparte en DashAI y descarga solo su archivo.
- **Sin razonamiento:** el modo de razonamiento está desactivado, así que el modelo responde directo. Está pensado para RAG.
- **Uso parcial de la GPU:** el parámetro `gpu_layers` carga solo parte de las capas en la GPU cuando el modelo no cabe completo.

Xiaomi reporta mejoras sobre Qwen3.5-9B en benchmarks de agentes y de oficina, por ejemplo OfficeQA 9,0 -> 19,5 (razonamiento sobre grandes colecciones de documentos) y SWE Pro 32,0 -> 44,6. No publica benchmarks de contexto largo: evalúalo con tus propios documentos.

1. Instálalo desde la tienda de plugins de DashAI, o con `pip install --no-deps --target ~/.DashAI/plugins/py3.12 dashai-mimo-v26-distill-qwen9b-model-package`.
2. Reinicia DashAI.
3. Descarga desde DashAI la cuantización que quieras, de 4,3 GB (Q3_K_S) a 17,9 GB (BF16).

## License

- **Plugin code:** Apache-2.0 (see [LICENSE](LICENSE)).
- **Model weights:** MIT, under the terms of the [original model card](https://huggingface.co/XiaomiMiMo/MiMo-V2.6-Distill-Qwen-9B). The model card also lists use restrictions (e.g. no unlawful or military use, and no autonomous high-risk actions without human oversight); read it before use.
- **Licencia de los pesos:** MIT, según la [ficha original del modelo](https://huggingface.co/XiaomiMiMo/MiMo-V2.6-Distill-Qwen-9B). La ficha del modelo además lista restricciones de uso (por ejemplo, nada ilegal ni militar, y ninguna acción autónoma de alto riesgo sin supervisión humana); léela antes de usarlo.
