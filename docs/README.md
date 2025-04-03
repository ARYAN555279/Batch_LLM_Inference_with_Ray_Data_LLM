
References

1. [Linkedin annoncement](https://www.linkedin.com/posts/robert-nishihara-b6465444_new-native-llm-apis-in-ray-data-and-ray-activity-7313348349699440643-R_aN/?utm_medium=ios_app&rcm=ACoAAAnEjvEBwXqQuBVVjxXuQ3cffPucbl2WqbM&utm_source=social_share_send&utm_campaign=copy_link)
3. [Announcing Native LLM APIs in Ray Data and Ray Serve](https://www.anyscale.com/blog/llm-apis-ray-data-serve)



[Robert Nishihara**Robert Nishihara**• 2nd**2nd**Co-founder at Anyscale**Co-founder at Anyscale**](https://www.linkedin.com/in/robert-nishihara-b6465444?miniProfileUrn=urn%3Ali%3Afsd_profile%3AACoAAAlZvnwBU_2hc9u5bqpEN7IL4B2SvrM8SUA)4h •**4 hours ago • Visible to anyone on or off LinkedIn**

Following

If you're using vLLM + Ray for batch inference or online serving, check this out. We're investing heavily in making that combination work really well.

[![View Kourosh Hakhamaneshi’s  graphic link](https://media.licdn.com/dms/image/v2/D5603AQF36E6WYkbyzQ/profile-displayphoto-shrink_100_100/profile-displayphoto-shrink_100_100/0/1684770674826?e=1749081600&v=beta&t=nF_D_Evri3eL7qQ-BJ9v8-VQb0D7VrwXyWGQhINdScs)](https://www.linkedin.com/in/kourosh-hakhamaneshi-4816a58a?miniProfileUrn=urn%3Ali%3Afsd_profile%3AACoAABL3tmEBb4cN7VJlMZ229OA8g5USo_tvda8)[Kourosh Hakhamaneshi**Kourosh Hakhamaneshi**• 2nd**2nd**AI lead @Anyscale, PhD UC Berkeley**AI lead @Anyscale, PhD UC Berkeley**](https://www.linkedin.com/in/kourosh-hakhamaneshi-4816a58a?miniProfileUrn=urn%3Ali%3Afsd_profile%3AACoAABL3tmEBb4cN7VJlMZ229OA8g5USo_tvda8&lipi=urn%3Ali%3Apage%3Ad_flagship3_detail_base%3BgVDzxsV6T92dpiq6NCLmHw%3D%3D)9h •**9 hours ago • Visible to anyone on or off LinkedIn**

Following

[](https://www.linkedin.com/feed/update/urn:li:activity:7313262118726705155/)[](https://www.linkedin.com/feed/update/urn:li:activity:7313262118726705155/)[Announcing native LLM APIs in Ray Data and Ray Serve Libraries. These are experimental APIs we are announcing today that abstract two things:1. Serve LLM: simplifies the deployment of LLM engines (e.g. vLLM) through ray serve APIs. Enables things like auto-scaling, monitoring, LoRA management, resource allocation etc.2. Data LLM: Helps you scale up offline inference horizontally for throughput sensitive applications (e.g. data curation, evaluation, etc). Ray data&#39;s lazy execution engine helps you pipeline complex heterogenous stages that involve LLMs. Say you want to create a pipeline that reformats input with Llama-8B and then queries Llama-70B in another stage. How do you maximize throughput for this pipeline? Or a vision language model that needs to pull images from s3 (a network bounded operation), tokenization (a cpu bounded op) and then inference with Pixtral (a gpu bounded op). This is the type of problem that Data LLM API will simplify.](https://www.linkedin.com/feed/update/urn:li:activity:7313262118726705155/)[https://lnkd.in/gSnAs4kf](https://lnkd.in/gSnAs4kf)

[New: Native LLM APIs in Ray Data and Ray Serve](https://www.anyscale.com/blog/llm-apis-ray-data-serve)



# Announcing Native LLM APIs in Ray Data and Ray Serve

By [The Anyscale Team](https://www.anyscale.com/blog?author=anyscale-team)   |   April 2, 2025

### Introduction

Today, we're excited to announce native APIs for LLM inference with [Ray Data](https://www.anyscale.com/product/library/ray-data) and [Ray Serve](https://www.anyscale.com/product/library/ray-serve).

As LLMs become increasingly central to modern AI infrastructure deployments, platforms require the ability to deploy and scale these models efficiently. While Ray Data and Ray Serve are suitable for this purpose, developers have to write a sizable amount of boilerplate in order to leverage the libraries for scaling LLM applications.

In Ray 2.44, we’re announcing **Ray Data LLM** and  **Ray Serve LLM** .

* **Ray Data LLM **provides APIs for offline batch inference with LLMs within existing Ray Data pipelines
* **Ray Serve LLM** provides APIs for deploying LLMs for online inference in Ray Serve applications.

Both modules offer first-class integration for [vLLM](https://github.com/vllm-project/vllm) and OpenAI compatible endpoints.

### Ray Data LLM

The `ray.data.llm` module integrates with key large language model (LLM) inference engines and deployed models to enable LLM batch inference.

Ray Data LLM is designed to address several common developer pains around batch inference:

* We saw that many users were building ad-hoc solutions for high-throughput batch inference. These solutions would entail launching many online inference servers and build extra proxying/load balancing utilities to maximize throughput. To address this, we wanted to leverage Ray Data and take advantage of pre-built distributed data loading and processing functionality.
* We saw common patterns of users sending batch data to an existing inference server. To address this, we wanted to make sure that users could integrate their data pipelines with an OpenAI compatible API endpoint, and provide the flexibility for the user to be able to templatize the query sent to the server.
* We saw that users were integrating LLMs into existing Ray Data pipelines (chaining LLM post-processing stages). To address this, we wanted to make sure that the API was compatible with the existing lazy and functional Ray Data API.

![rayllm-1](https://images.ctfassets.net/xjan103pcp94/1um7fuSPANnhHfuEppQaUK/9c8fc0ea312244edb570aec4f76a242a/rayllm-1.png "rayllm-1")
rayllm-1

With Ray Data LLM, users create a Processor object, which can be called on a Ray Data Dataset and will return a Ray Data dataset. The processor object will contain configuration like:

* Prompt and template
* OpenAI compatible sampling parameters, which can be specified per row
* vLLM engine configuration, if applicable

```bash
1import ray
2from ray.data.llm import vLLMEngineProcessorConfig, build_llm_processor
3import numpy as np
4
5config = vLLMEngineProcessorConfig(
6    model="meta-llama/Llama-3.1-8B-Instruct",
7    engine_kwargs={
8"enable_chunked_prefill": True,
9"max_num_batched_tokens": 4096,
10"max_model_len": 16384,
11    },
12    concurrency=1,
13    batch_size=64,
14)
15processor = build_llm_processor(
16    config,
17    preprocess=lambda row: dict(
18        messages=[
19            {"role": "system", "content": "You are a bot that responds with haikus."},
20            {"role": "user", "content": row["item"]}
21        ],
22        sampling_params=dict(
23            temperature=0.3,
24            max_tokens=250,
25        )
26    ),
27    postprocess=lambda row: dict(
28        answer=row["generated_text"],
29        **row  # This will return all the original columns in the dataset.
30    ),
31)
32
33ds = ray.data.from_items(["Start of the haiku is: Complete this for me..."])
34
35ds = processor(ds)
36ds.show(limit=1)
```

In this particular example, the Processor object will:

* Perform the necessary preprocessing and postprocessing to handle LLM outputs properly
* Instantiate and configure multiple vLLM replicas, depending on specified concurrency and provided engine configurations. Each of these replicas can themselves be distributed as well.
* Continuously feed each replica by leveraging async actors in Ray to take advantage of continuous batching and maximize throughput
* Invoke various Ray Data methods (`map`, `map_batches`) which can be fused and optimized with other preprocessing stages in the pipeline by Ray Data during execution.

As you can see, Ray Data LLM can easily simplify the usage of LLMs within your existing data pipelines. See the [documentation](https://docs.ray.io/en/latest/data/working-with-llms.html?__hstc=80633672.c91330b497c1b083e453a5e603aa9318.1743652956766.1743652956766.1743652956766.1&__hssc=80633672.2.1743652956766&__hsfp=2850574025) for more details.

### Ray Serve LLM

Ray Serve LLM APIs allow users to deploy multiple LLM models together with a familiar Ray Serve API, while providing compatibility with the OpenAI API.

Ray Serve LLM is designed with the following features:

* Automatic scaling and load balancing
* Unified multi-node multi-model deployment
* OpenAI compatibility
* Multi-LoRA support with shared base models
* Deep integration with inference engines (vLLM to start)
* Composable multi-model LLM pipelines

While vLLM has grown rapidly over the last year, we have seen a significant uptick of users leveraging Ray Serve to deploy vLLM for multiple models and program more complex pipelines.

For production deployments, Ray Serve + vLLM are great complements.

![RayServe-vLLM](https://images.ctfassets.net/xjan103pcp94/1EICFimfVjyzRij2fOZvGM/cf156075c5e5624bbe4cd4e00c35b147/image1.png "RayServe-vLLM")
RayServe-vLLM

vLLM provides a simple abstraction layer to serve hundreds of different models with high throughput and low latency. However, vLLM is only responsible for single model replicas, and for production deployments you often need an orchestration layer to be able to autoscale, handle different fine-tuned adapters, handle distributed model-parallelism, and author multi-model, compound AI pipelines that can be quite complex.

Ray Serve is built to address the gaps that vLLM has for scaling and productionization. Ray Serve offers:

* Pythonic API for autoscaling
* Built-in support for model multiplexing
* Provides a Pythonic, imperative way to write complex multi-model / deployment pipelines
* Has first-class support for distributed model parallelism by leveraging Ray.

Below is a simple example of deploying a Qwen model with Ray Serve on a local machine with two GPUs behind an OpenAI-compatible router, then querying it with the OpenAI client.

```bash
1from ray import serve
2from ray.serve.llm import LLMConfig, LLMServer, LLMRouter
3
4llm_config = LLMConfig(
5    model_loading_config=dict(
6        model_id="qwen-0.5b",
7        model_source="Qwen/Qwen2.5-0.5B-Instruct",
8    ),
9    deployment_config=dict(
10        autoscaling_config=dict(
11            min_replicas=1, max_replicas=2,
12        )
13    ),
14# Pass the desired accelerator type (e.g. A10G, L4, etc.)
15    accelerator_type="A10G",
16# You can customize the engine arguments (e.g. vLLM engine kwargs)
17    engine_kwargs=dict(
18        tensor_parallel_size=2,
19    ),
20)
21
22# Deploy the application
23deployment = LLMServer.as_deployment(
24    llm_config.get_serve_options(name_prefix="vLLM:")).bind(llm_config)
25llm_app = LLMRouter.as_deployment().bind([deployment])
26serve.run(llm_app)
```

And then you can query this with the OpenAI Python API:

```bash
1from openai import OpenAI
2
3# Initialize client
4client = OpenAI(base_url="http://localhost:8000/v1", api_key="fake-key")
5
6# Basic chat completion with streaming
7response = client.chat.completions.create(
8    model="qwen-0.5b",
9    messages=[{"role": "user", "content": "Hello!"}],
10    stream=True
11)
12
13for chunk in response:
14if chunk.choices[0].delta.content is not None:
15print(chunk.choices[0].delta.content, end="", flush=True)
16
```

Visit [the documentation](https://docs.ray.io/en/master/serve/llm/serving-llms.html?__hstc=80633672.c91330b497c1b083e453a5e603aa9318.1743652956766.1743652956766.1743652956766.1&__hssc=80633672.2.1743652956766&__hsfp=2850574025) for more details.

Ray Serve LLM can also be deployed on Kubernetes by using KubeRay. Take a look at the [Ray Serve production guide](https://docs.ray.io/en/master/serve/production-guide/index.html?__hstc=80633672.c91330b497c1b083e453a5e603aa9318.1743652956766.1743652956766.1743652956766.1&__hssc=80633672.2.1743652956766&__hsfp=2850574025) for more details

### Future Developments

Give these new features a spin and let us know your feedback! If you're interested in chatting with developers, feel free to join [the Ray Slack](https://www.ray.io/join-slack?__hstc=80633672.c91330b497c1b083e453a5e603aa9318.1743652956766.1743652956766.1743652956766.1&__hssc=80633672.2.1743652956766&__hsfp=2850574025) or participate on [Discourse](https://discuss.ray.io/?__hstc=80633672.c91330b497c1b083e453a5e603aa9318.1743652956766.1743652956766.1743652956766.1&__hssc=80633672.2.1743652956766&__hsfp=2850574025), and follow the roadmap for Ray Serve LLM and Ray Data LLM [here](https://github.com/ray-project/ray/issues/51313) for future updates.
