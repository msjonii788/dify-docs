# Connecting to OpenLLM Local Deployed Models

With [OpenLLM](https://github.com/bentoml/OpenLLM), you can run inference with any open-source large-language models, deploy to the cloud or on-premises, and build powerful AI apps.
And Dify supports connecting to OpenLLM deployed large language model's inference capabilities locally.

## Deploy OpenLLM Model

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

### Starting OpenLLM

Each OpenLLM Server can deploy one model, and you can deploy it in the following way:

1. First, install OpenLLM through PyPI:

    ```bash
    $ pip install openllm
    ```

2. Locally deploy and start the OpenLLM model:

    ```bash
    $ openllm start opt --model_id facebook/opt-125m -p 3333
    2023-08-20T23:49:59+0800 [INFO] [cli] Prometheus metrics for HTTP BentoServer from "_service:svc" can be accessed at http://localhost:3333/metrics.
    2023-08-20T23:50:00+0800 [INFO] [cli] Starting production HTTP BentoServer from "_service:svc" listening on http://0.0.0.0:3333 (Press CTRL+C to quit)
    ```
   
    After OpenLLM starts, it provides API access service for the local port `3333`, the endpoint being: `http://127.0.0.1:3333`. Since the default 3000 port conflicts with Dify's WEB service, the port is changed to 3333 here.
    If you need to modify the host or port, you can view the help information for starting OpenLLM: `openllm start opt --model_id facebook/opt-125m -h`.

    > Note: Using the `facebook/opt-125m` model here is only for demonstration, and the effect may not be good. Please choose the appropriate model according to the actual situation. For more models, please refer to: [Supported Model List](https://github.com/bentoml/OpenLLM#-supported-models).

3. After the model is deployed, use the connected model in Dify.

   Fill in under `Settings > Model Providers > OpenLLM`:

   - Model Name: `facebook/opt-125m`
   - Server URL: `http://127.0.0.1:3333`

   Click "Save" and the model can be used in the application.

This instruction is only for quick connection as an example. For more features and information on using OpenLLM, please refer to: [OpenLLM](https://github.com/bentoml/OpenLLM)