# Connecting to Xinference Local Deployed Models

[Xorbits inference](https://github.com/xorbitsai/inference) is a powerful and versatile library designed to serve language, speech recognition, and multimodal models, and can even be used on laptops. It supports various models compatible with GGML, such as chatglm, baichuan, whisper, vicuna, orca, etc.
And Dify supports connecting to Xinference deployed large language model inference and embedding capabilities locally.

## Deploy Xinference

### Before you start

When using Docker to deploy a private model locally, you might need to access the service via the container's IP address instead of `127.0.0.1`. This is because `127.0.0.1` or `localhost` by default points to your host system and not the internal network of the Docker container. To retrieve the IP address of your Docker container, you can follow these steps:

1. First, determine the name or ID of your Docker container. You can list all active containers using the following command:

```bash
docker ps
```

2. Then, use the command below to obtain detailed information about a specific container, including its IP address:

```bash
docker inspect -f '{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}' container_name_or_ID
```

Please note that you usually do not need to manually find the IP address of the Docker container to access the service, because Docker offers a port mapping feature. This allows you to map the container ports to local machine ports, enabling access via your local address. For example, if you used the `-p 80:80` parameter when running the container, you can access the service inside the container by visiting `http://localhost:80` or `http://127.0.0.1:80`.

If you do need to use the container's IP address directly, the steps above will assist you in obtaining this information.

### Starting Xinference

There are two ways to deploy Xinference, namely [local deployment](https://github.com/xorbitsai/inference/blob/main/README.md#local) and [distributed deployment](https://github.com/xorbitsai/inference/blob/main/README.md#distributed), here we take local deployment as an example.

1. First, install Xinference via PyPI:

    ```bash
    $ pip install "xinference[all]"
    ```

2. Start Xinference locally:

    ```bash
    $ xinference
    2023-08-20 19:21:05,265 xinference   10148 INFO     Xinference successfully started. Endpoint: http://127.0.0.1:9997
    2023-08-20 19:21:05,266 xinference.core.supervisor 10148 INFO     Worker 127.0.0.1:37822 has been added successfully
    2023-08-20 19:21:05,267 xinference.deploy.worker 10148 INFO     Xinference worker successfully started.
    ```

    Xinference will start a worker locally by default, with the endpoint: `http://127.0.0.1:9997`, and the default port is `9997`.
    By default, access is limited to the local machine only, but it can be configured with `-H 0.0.0.0` to allow access from any non-local client.
    To modify the host or port, you can refer to xinference's help information: `xinference --help`.

3. Create and deploy the model

    Visit `http://127.0.0.1:9997`, select the model and specification you need to deploy, and click `Create` to create and deploy the model, as shown below:

    <figure><img src="../../.gitbook/assets/xinference-webpage.png" alt=""><figcaption></figcaption></figure>

    As different models have different compatibility on different hardware platforms, please refer to [Xinference built-in models](https://inference.readthedocs.io/en/latest/models/builtin/index.html) to ensure the created model supports the current hardware platform.

4. Obtain the model UID

    Return to the command line interface and enter:

    ```bash
    $ xinference list
    UID                                   Type    Name         Format      Size (in billions)  Quantization
    ------------------------------------  ------  -----------  --------  --------------------  --------------
    a9e4d530-3f4b-11ee-a9b9-e6608f0bd69a  LLM     vicuna-v1.3  ggmlv3                       7  q2_K
    ```
   
    The first column is the model UID created in step 3, such as `a9e4d530-3f4b-11ee-a9b9-e6608f0bd69a` above.

5. After the model is deployed, connect the deployed model in Dify.

   In `Settings > Model Providers > Xinference`, enter:

   - Model name: `vicuna-v1.3`
   - Server URL: `http://127.0.0.1:9997`
   - Model UID: `a9e4d530-3f4b-11ee-a9b9-e6608f0bd69a`

   Click "Save" to use the model in the dify application.

Dify also supports using [Xinference builtin models](https://github.com/xorbitsai/inference/blob/main/README.md#builtin-models) as Embedding models, just select the Embeddings type in the configuration box.

For more information about Xinference, please refer to: [Xorbits Inference](https://github.com/xorbitsai/inference)