# 接入 Xinference 部署的本地模型

[Xorbits inference](https://github.com/xorbitsai/inference) 是一个强大且通用的分布式推理框架，旨在为大型语言模型、语音识别模型和多模态模型提供服务，甚至可以在笔记本电脑上使用。它支持多种与GGML兼容的模型,如 chatglm, baichuan, whisper, vicuna, orca 等。
Dify 支持以本地部署的方式接入 Xinference 部署的大型语言模型推理和 embedding 能力。

## 部署 Xinference

### 使用前注意事项

当您使用 Docker 在本地部署一个私有模型时，您可能需要通过容器的 IP 地址而不是 `127.0.0.1` 来访问该服务。这是因为 `127.0.0.1` 或 `localhost` 默认指向您的主机系统，而不是 Docker 容器的内部网络。为了获取 Docker 容器的 IP 地址，您可以按照以下步骤操作：

1. 首先，确定您的 Docker 容器的名称或 ID。您可以使用以下命令列出所有运行中的容器：

```bash
docker ps
```

2. 然后，使用以下命令获取指定容器的详细信息，包括其 IP 地址：

```bash
docker inspect -f '{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}' 容器名称或ID
```

请注意，您通常不需要手动寻找 Docker 容器的 IP 地址来访问服务，因为 Docker 提供了端口映射功能，允许您将容器端口映射到本机端口，从而通过本机地址进行访问。例如，如果您在运行容器时使用了 `-p 80:80` 参数，那么您可以通过访问 `http://localhost:80` 或 `http://127.0.0.1:80` 来访问容器中的服务。

如果确实需要直接使用容器的 IP 地址，以上步骤将帮助您获取到这一信息。

### 开始部署

部署 Xinference 有两种方式，分别为[本地部署](https://github.com/xorbitsai/inference/blob/main/README_zh_CN.md#%E6%9C%AC%E5%9C%B0%E9%83%A8%E7%BD%B2)和[分布式部署](https://github.com/xorbitsai/inference/blob/main/README_zh_CN.md#%E5%88%86%E5%B8%83%E5%BC%8F%E9%83%A8%E7%BD%B2)，以下以本地部署为例。

1. 首先通过 PyPI 安装 Xinference：

    ```bash
    $ pip install "xinference[all]"
    ```

2. 本地部署方式启动 Xinference：

    ```bash
    $ xinference
    2023-08-20 19:21:05,265 xinference   10148 INFO     Xinference successfully started. Endpoint: http://127.0.0.1:9997
    2023-08-20 19:21:05,266 xinference.core.supervisor 10148 INFO     Worker 127.0.0.1:37822 has been added successfully
    2023-08-20 19:21:05,267 xinference.deploy.worker 10148 INFO     Xinference worker successfully started.
    ```
   
    Xinference 默认会在本地启动一个 worker，端点为：`http://127.0.0.1:9997`，端口默认为 `9997`。
    默认只可本机访问，可配置 `-H 0.0.0.0`，非本地客户端可任意访问。
    如需进一步修改 host 或 port，可查看 xinference 的帮助信息：`xinference --help`。

3. 创建并部署模型
    
    进入 `http://127.0.0.1:9997` 选择需要部署的模型和规格，点击 `Create` 即可创建并部署模型，如下图所示：

    <figure><img src="../../.gitbook/assets/xinference-webpage.png" alt=""><figcaption></figcaption></figure>
   
   由于不同模型在不同硬件平台兼容性不同，请查看 [Xinference 内置模型](https://inference.readthedocs.io/en/latest/models/builtin/index.html) 确定创建的模型是否支持当前硬件平台。

4. 获取模型 UID

   回到命令行界面，输入：

    ```bash
    $ xinference list
    UID                                   Type    Name         Format      Size (in billions)  Quantization
    ------------------------------------  ------  -----------  --------  --------------------  --------------
    a9e4d530-3f4b-11ee-a9b9-e6608f0bd69a  LLM     vicuna-v1.3  ggmlv3                       7  q2_K
   ```
   
   第一列即为第 3 步创建的模型 UID，如上面为 `a9e4d530-3f4b-11ee-a9b9-e6608f0bd69a`。

5. 模型部署完毕，在 Dify 中使用接入模型

   在 `设置 > 模型供应商 > Xinference` 中填入：

   - 模型名称：`vicuna-v1.3`
   - 服务器 URL：`http://127.0.0.1:9997`
   - 模型 UID：`a9e4d530-3f4b-11ee-a9b9-e6608f0bd69a`

   "保存" 后即可在应用中使用该模型。

Dify 同时支持将 [Xinference embed 模型](https://github.com/xorbitsai/inference/blob/main/README_zh_CN.md#%E5%86%85%E7%BD%AE%E6%A8%A1%E5%9E%8B) 作为 Embedding 模型使用，只需在配置框中选择 `Embeddings` 类型即可。

如需获取 Xinference 更多信息，请参考：[Xorbits Inference](https://github.com/xorbitsai/inference/blob/main/README_zh_CN.md)