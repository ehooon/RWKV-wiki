# Ollama Inference

::: tip
[Ollama](https://github.com/ollama) is a simple and easy-to-use open-source local large language model running framework that supports one-click deployment and running of various open-source models on personal computers, featuring simple configuration and low resource consumption.
:::

With the efforts of RWKV community member [@MollySophia](https://github.com/MollySophia), Ollama now supports the RWKV-7 and RWKV-6 model.

This chapter introduces how to use the RWKV model for inference in Ollama.

## Download and Installation of Ollama

You can download the Ollama installer from the [Ollama official website](https://ollama.com/).

After downloading, double-click the exe file to install. After installation, `Ollama` will automatically start, and you can see the `Ollama` icon in the system taskbar.

![ollama-icon](./imgs/ollama-icon.png)

## Run RWKV model

There are two ways to run the gguf format RWKV model in Ollama:

- **Download from [Ollama's RWKV repository](https://ollama.com/mollysama)**: Simple to operate, but the RWKV model provided by Ollama **only has the `q4_k_m` quantized version**
- **Custom RWKV model**: Requires manually downloading the `gguf` format RWKV model and creating a `Modelfile` configuration file, but you can **freely run any quantized RWKV model**

::: tabs

@tab Download from Ollama repository
[Ollama's RWKV repository](https://ollama.com/mollysama) provides the RWKV7-G1 (recommended), RWKV7-World, and RWKV-6 models.

![ollama-rwkv-7-model-repo](./imgs/ollama-rwkv-7-model-repo.png)

Execute the command `ollama run mollysama/rwkv-7-world:2.9b` in your terminal to automatically **download and run** the `q4_k_m` quantized version of the RWKV-7-World 2.9B model.

![ollama-run-rwkv-7-world-2.9b](./imgs/ollama-run-rwkv-7-world-2.9b.png)

::: tip
Ollama has not yet adapted to support inference for the RWKV7-G1 series models.

@tab Custom RWKV-7 model

### Download RWKV gguf model

To customize the RWKV model, you need to first download the `gguf` format RWKV-7-World model from the [RWKV-7 GGUF repository](https://huggingface.co/collections/zhiyuan8/rwkv-7-world-gguf-67dbb8f4baeae23ec791f49e).

---
⚠️ **RWKV gguf models come in various quantized versions. It is recommended to use `Q5_1` or `Q8_0` quantization precision. Lower quantization precision (such as `Q4_0`, `Q3_0`, etc.) may significantly degrade the model's responses.**

---

If you have fine-tuned an RWKV-7 model and wish to convert it from pth to gguf format, see the [llama.cpp documentation](./llamacpp.html#obtain-gguf-format-models).

---

### Create Modelfile for the model

Create a text file named `Modelfile` in the folder where the RWKV gguf model file is stored, without any file extension.

![Modelfile](./imgs/ollama-Modelfile.png)

Then open this text file with a text editor like "Notepad" and write the following content:

```bash copy
FROM rwkv7-1.5B-world-Q8_0.gguf

TEMPLATE """
{{- range .Messages }}
{{- if eq .Role "user" }}User: {{ .Content }}{{- else if eq .Role "assistant" }}Assistant:{{ .Content }}{{- end }}

{{ end }}Assistant:"""

PARAMETER stop "\n\n"
PARAMETER stop "\nUser:"
PARAMETER stop "User:"
PARAMETER stop "<s>"
```

Modify the `rwkv7-1.5B-world-Q8_0.gguf` after the first line `FROM` to the filename of the RWKV model you downloaded.

---
⚠️ **It is recommended to directly copy the above content into the `Modelfile` to ensure there is a space after `User:` and no space after `Assistant:`; there is an empty line above `{{ end }}Assistant:"""` and no extra characters after it.**

---

![Modelfile-RWKV-7](./imgs/ollama-Modelfile-content-rwkv7.jpg)

### Run custom RWKV model

Open the terminal in the RWKV gguf model folder and execute the `ollama create` command:

``` bash copy
ollama create rwkv-7-world-1.5b-Q8_0 -f Modelfile
```

---
Change the model name after `ollama create` to your local RWKV model, ensuring it matches the model name in the `Modelfile`.

---
![ollama-create](./imgs/ollama-create.png)

After creation, use the `ollama run` command to directly run the model:

``` bash copy
ollama run rwkv-7-world-1.5b-Q8_0
```

After successfully running, you can chat with the model:

![ollama-chat](./imgs/ollama-chat-rwkv7.png)

@tab Custom RWKV-6 model

To customize the RWKV model, you need to first download the `gguf` format RWKV-6-World model from the [RWKV-6 GGUF repository](https://huggingface.co/collections/zhiyuan8/rwkv-6-world-gguf-6790b87a42d4f541a2f6d92b).

---
⚠️ **RWKV gguf models come in various quantized versions. It is recommended to use `Q5_1` or `Q8_0` quantization precision. Lower quantization precision (such as `Q4_0`, `Q3_0`, etc.) may significantly degrade the model's responses.**

---

### Create Modelfile for the model

Create a text file named `Modelfile` in the folder where the RWKV gguf model file is stored, without any file extension.

![Modelfile](./imgs/ollama-Modelfile.png)

Then open this text file with a text editor like "Notepad" and write the following content:

```bash copy
FROM rwkv-6-world-1.6b-Q8_0.gguf

TEMPLATE """
{{- range .Messages }}
{{- if eq .Role "user" }}User: 
{{- else if eq .Role "assistant" }}Assistant:
{{- end }}{{ .Content }}

{{ end }}Assistant:"""

PARAMETER stop "\n\n"
PARAMETER stop "\nUser:"
```

Modify the `rwkv-6-world-1.6b-Q8_0.gguf` after the first line `FROM` to the filename of the RWKV model you downloaded.

---
⚠️ **It is recommended to directly copy the above content into the `Modelfile` to ensure there is a space after `User:` and no space after `Assistant:`; there is an empty line above `{{ end }}Assistant:"""` and no extra characters after it.**

---

![Modelfile](./imgs/ollama-Modelfile-content.png)

### Run custom RWKV model

Open the terminal in the RWKV gguf model folder and execute the `ollama create` command:

``` bash copy
ollama create rwkv-6-world-1.6b-Q8_0 -f Modelfile
```

---
Change the model name after `ollama create` to your local RWKV model, ensuring it matches the model name in the `Modelfile`.

---
![ollama-create](./imgs/ollama-create.png)

After creation, use the `ollama run` command to directly run the model:

``` bash copy
ollama run rwkv-6-world-1.6b-Q8_0
```

After successfully running, you can chat with the model:

![ollama-chat](./imgs/ollama-chat.png)

:::

## Ollama GUI and Desktop Programs

Ollama itself does not provide GUI or WebUI services, but its community offers third-party GUI and desktop programs.

You can view all third-party Ollama tools in the [Ollama GitHub documentation](https://github.com/ollama/ollama?tab=readme-ov-file#web--desktop).

## References

- [Ollama official website](https://ollama.com/)
- [RWKV gguf model repository](https://huggingface.co/latestissue)
- [Ollama GitHub documentation](https://github.com/ollama/ollama?tab=readme-ov-file#web--desktop)