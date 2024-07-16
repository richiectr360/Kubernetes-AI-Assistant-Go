# Kubectl-assistant OpenAI plugin ✨

## Overview

`kubectl-assistant` is a CLI tool designed to help Kubernetes users generate and apply Kubernetes YAML manifests using OpenAI's GPT API. The tool simplifies the process of creating and managing Kubernetes resources by leveraging the power of large language models.

### Project Architecture

Enter this link:
https://whimsical.com/kubernetes-ai-assistant-QQRMXwY9mJpAni9bSUuXXm

Password:
kubernetesaiassistant

## Problem Statement

Managing Kubernetes resources can be complex and error-prone. Writing and maintaining Kubernetes YAML manifests manually requires in-depth knowledge of Kubernetes specifications and can be time-consuming. This complexity poses a significant challenge, especially for users who are not well-versed in Kubernetes.

### Problems the Project Solve

The `kubectl-assistant` project addresses the following key problems:

1. Complexity in Kubernetes Configuration:

- Simplifies the process of creating and managing Kubernetes YAML manifests by allowing users to generate these configurations using natural language commands.

2. Error Reduction:

- Reduces the likelihood of errors in Kubernetes manifests by automating their generation, ensuring that the configurations adhere to best practices and standards.

3. Time Efficiency:

- Saves time for developers and DevOps engineers by providing a quick and easy way to generate Kubernetes manifests, eliminating the need to write and debug these files manually.

4. Enhanced Developer Productivity:

- By automating repetitive and complex tasks, the project allows developers to focus more on coding and less on configuration management.

5. User-Friendly Interface:

- Provides a command-line interface (CLI) that is intuitive and easy to use, making Kubernetes more accessible to users with varying levels of expertise.

## Technical Solution

`kubectl-assistant` provides an easy-to-use CLI that integrates with OpenAI's GPT API to generate accurate and valid Kubernetes YAML manifests. The tool offers the following features:

- Generates Kubernetes YAML manifests based on user prompts.
- Optionally applies the generated manifests directly to a Kubernetes cluster.
- Supports both OpenAI and Azure OpenAI Service.
- Handles Kubernetes API schema retrieval to ensure the manifests are up-to-date.
- Reduces potential errors and saves time for developers and DevOps engineers.

This is an A.I. powered `kubectl` plugin to generate and apply Kubernetes manifests using OpenAI GPT.

## Tech Stack

This project utilizes the following tools and libraries:

- Go (Golang): The primary programming language used for developing the CLI.
- Cobra: A library for creating powerful modern CLI applications.
- PromptUI: A library for creating interactive command-line prompts.
- Logrus: A structured logger for Go, used for logging within the application.
- Kubernetes Client-Go: The official Kubernetes client library for Go.
- OpenAI API: Provides access to OpenAI's language models.
- Azure OpenAI Service: Optional support for Azure's implementation of OpenAI services.
- Go-Retry: A library for retry logic.
- Go-Spinner: A library for adding spinner animations to the CLI.
- Grafana: Used for visualizing metrics and logs.
- Prometheus: Used for monitoring and alerting, collecting metrics from Kubernetes clusters.

## Technical Details

`kubectl-assistant` requires a valid Kubernetes configuration, and make sure your config file is in ~.kube/ and the name of the file is config 
it also needs one of the following:

- [OpenAI API key](https://platform.openai.com/overview)
- [Azure OpenAI Service](https://aka.ms/azure-openai) API key and endpoint
- [Local AI](https://github.com/go-skynet/LocalAI) (see [getting started](https://localai.io/basics/getting_started/index.html))

For OpenAI, Azure OpenAI or LocalAI, you can use the following environment variables:
make sure you use the latest 3.5 turbo model to ensure you get to GPT functions

```shell
export OPENAI_API_KEY=<your OpenAI key>
export OPENAI_DEPLOYMENT_NAME=gpt-3.5-turbo-1106
export OPENAI_ENDPOINT=<your OpenAI endpoint, like "https://my-aoi-endpoint.openai.azure.com" or "http://localhost:8080/v1">
```

If `OPENAI_ENDPOINT` variable is set, then it will use the endpoint. Otherwise, it will use OpenAI API.

Azure OpenAI service does not allow certain characters, such as `.`, in the deployment name. Consequently, `kubectl-assistant` will automatically replace `gpt-3.5-turbo` to `gpt-35-turbo` for Azure. However, if you use an Azure OpenAI deployment name completely different from the model name, you can set `AZURE_OPENAI_MAP` environment variable to map the model name to the Azure OpenAI deployment name. For example:

```shell
export AZURE_OPENAI_MAP="gpt-3.5-turbo=my-deployment"
```

### Flags and environment variables

- `--require-confirmation` flag or `REQUIRE_CONFIRMATION` environment varible can be set to prompt the user for confirmation before applying the manifest. Defaults to true.

- `--temperature` flag or `TEMPERATURE` environment variable can be set between 0 and 1. Higher temperature will result in more creative completions. Lower temperature will result in more deterministic completions. Defaults to 0.

- `--use-k8s-api` flag or `USE_K8S_API` environment variable can be set to use Kubernetes OpenAPI Spec to generate the manifest. This will result in very accurate completions including CRDs (if present in configured cluster). This setting will use more OpenAI API calls and it requires [function calling](https://openai.com/blog/function-calling-and-other-api-updates) which is available in `0613` or later models only. Defaults to false. However, this is recommended for accuracy and completeness.

- `--k8s-openapi-url` flag or `K8S_OPENAPI_URL` environment variable can be set to use a custom Kubernetes OpenAPI Spec URL. This is only used if `--use-k8s-api` is set. By default, `kubectl-assistant` will use the configured Kubernetes API Server to get the spec unless this setting is configured. You can use the [default Kubernetes OpenAPI Spec](https://raw.githubusercontent.com/kubernetes/kubernetes/master/api/openapi-spec/swagger.json) or generate a custom spec for completions that includes custom resource definitions (CRDs). You can generate custom OpenAPI Spec by using `kubectl get --raw /openapi/v2 > swagger.json`.

## Examples

### Creating objects with specific values

```shell
$ go run main.go "create an nginx deployment with 3 replicas"
✨ Attempting to apply the following manifest:
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
  labels:
    app: nginx
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:1.7.9
        ports:
        - containerPort: 80
Use the arrow keys to navigate: ↓ ↑ → ←
? Would you like to apply this? [Reprompt/Apply/Don't Apply]:
+   Reprompt
  ▸ Apply
    Don't Apply
```

### Reprompt to refine your prompt

```shell
...
Reprompt: update to 5 replicas and port 8080
✨ Attempting to apply the following manifest:
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
  labels:
    app: nginx
spec:
  replicas: 5
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:1.7.9
        ports:
        - containerPort: 8080
Use the arrow keys to navigate: ↓ ↑ → ←
? Would you like to apply this? [Reprompt/Apply/Don't Apply]:
+   Reprompt
  ▸ Apply
    Don't Apply
```

### Multiple objects

```shell
$ go run main.go "create a foo namespace then create nginx pod in that namespace"
✨ Attempting to apply the following manifest:
apiVersion: v1
kind: Namespace
metadata:
  name: foo
---
apiVersion: v1
kind: Pod
metadata:
  name: nginx
  namespace: foo
spec:
  containers:
  - name: nginx
    image: nginx:latest
Use the arrow keys to navigate: ↓ ↑ → ←
? Would you like to apply this? [Reprompt/Apply/Don't Apply]:
+   Reprompt
  ▸ Apply
    Don't Apply
```

### Optional `--require-confirmation` flag

```shell
$ go run main.go "create a service with type LoadBalancer with selector as 'app:nginx'" --require-confirmation=false
✨ Attempting to apply the following manifest:
apiVersion: v1
kind: Service
metadata:
  name: nginx-service
spec:
  selector:
    app: nginx
  ports:
  - port: 80
    targetPort: 80
  type: LoadBalancer
```

> Please note that the plugin does not know the current state of the cluster (yet?), so it will always generate the full manifest.
