<div align="center">
    <img src="figures/logo.png" alt="logo" width="50%">
</div>

# Batch LLM Inference with Ray Data LLM

## Table of Contents

<details>
  <summary><a href="#1-introduction"><i><b>1. Introduction</b></i></a></summary>
  <div>
              <a href="#11-ray">1.1. Ray</a><br>
              <a href="#12-ray-data-llm">1.2. Ray Data LLM</a><br>
              <a href="#13-problem-generate-responses-to-common-questions">1.3. Problem: Generate Responses to Common Questions</a><br>
              <a href="#14-what-you-want">1.4. What You Want</a><br>
              <a href="#15-how-ray-data-llm-helps">1.5. How Ray Data LLM Helps</a><br>
              <a href="#16-why-use-ray-data-llm">1.6. Why Use Ray Data LLM</a><br>
              <a href="#17-next-steps">1.7. Next Steps</a><br>
  </div>
</details>

<details>
  <summary><a href="#2-fundamental-level-generating-responses-to-common-questions"><i><b>2. Fundamental Level: Generating Responses to Common Questions</b></i></a></summary>
  <div>
              <a href="#21-problem">2.1. Problem</a><br>
              <a href="#22-example-questions">2.2. Example Questions</a><br>
              <a href="#23-why-batch-processing">2.3. Why Batch Processing</a><br>
              <a href="#24-real-life-example">2.4. Real-Life Example</a><br>
  </div>
</details>

<details>
  <summary><a href="#3-intermediate-level-text-generation-from-user-prompts"><i><b>3. Intermediate Level: Text Generation from User Prompts</b></i></a></summary>
  <div>
              <a href="#31-problem">3.1. Problem</a><br>
              <a href="#32-example-prompts">3.2. Example Prompts</a><br>
              <a href="#33-why-use-ray-data-llm">3.3. Why Use Ray Data LLM</a><br>
  </div>
</details>

<details>
  <summary><a href="#4-advanced-level-real-world-use-case"><i><b>4. Advanced Level: Real-World Use Case</b></i></a></summary>
  <div>
              <a href="#41-problem">4.1. Problem</a><br>
              <a href="#42-why-batch-processing">4.2. Why Batch Processing</a><br>
              <a href="#43-how-ray-data-llm-helps">4.3. How Ray Data LLM Helps</a><br>
  </div>
</details>

## 1. Introduction

Think about a customer support bot that receives 1000 similar questions every day. Instead of answering each one as it comes, you could collect the questions and process them in bulk (Batch processing), giving quick responses to all of them at once.

Batch processing is essential when generating text responses efficiently using large language models (LLMs). Instead of processing each input one by one, batch inference allows the handling of multiple inputs at once, making it faster and more scalable.

In this tutorial, we will start with a straightforward problem to introduce the concept of batch inference with Ray Data LLM. We will then progress to more advanced scenarios, gradually building your understanding and skills.

### 1.1. Ray

Ray is an open-source framework for building and running distributed applications. It enables developers to scale Python applications from a single machine to a cluster, making parallel computing more accessible. Ray provides a unified API for various tasks such as data processing, model training, and inference.

Key Features:

* Distributed computing made easy with Pythonic syntax
* Built-in support for machine learning workflows
* Libraries for hyperparameter tuning (Ray Tune), model serving (Ray Serve), and data processing (Ray Data)
* Scales seamlessly from a single machine to a large cluster

### 1.2. Ray Data LLM

Ray Data LLM is a module within Ray designed for batch inference using large language models (LLMs). It allows developers to perform batch text generation efficiently by integrating LLM processing into existing Ray Data pipelines.

Key Features:

* Batch Processing:
  * Simultaneous processing of multiple inputs for faster throughput
* Efficient Scaling:
  * Distributes tasks across multiple CPUs/GPUs
* Seamless Integration:
  * Works with popular LLMs like Meta Llama
* OpenAI Compatibility:
  * Easily integrates with OpenAI API endpoints

By using Ray Data LLM, developers can scale their LLM-based applications without the hassle of manual optimization or distributed computing setups.

### 1.3. Problem: Generate Responses to Common Questions

Imagine you have a list of common questions people might ask a chatbot. Instead of responding to each question one by one, you want to generate answers for all the questions at once.

#### 1.3.1. Example Questions:

1. "What is the weather like today?"
2. "Tell me a joke."
3. "Give me a motivational quote."
4. "What is 2+2?"

### 1.4. What You Want:

Instead of sending each question to the chatbot separately, you want to batch process them all at once, saving time and computing resources.

### 1.5. How Ray Data LLM Helps:

* **Batch Processing:** Instead of generating responses one by one, Ray Data LLM lets you process all questions simultaneously.
* **Efficient Scaling:** If you have 1000 questions, the system can distribute the workload across multiple processors, making it much faster.

### 1.6. Why Use Ray Data LLM?

Ray Data LLM integrates seamlessly with existing Ray Data pipelines, allowing for efficient batch inference with LLMs. It enables:

* High-throughput processing
* Distributed execution
* Integration with OpenAI-compatible endpoints
* Support for popular LLMs like Meta Llama

### 1.7. Next Steps

Ray Data LLM provides a simple and scalable way to perform batch inference using LLMs. Starting from basic chatbot responses to complex sentiment analysis, it enables high-throughput text generation and processing.

* Set up Ray and Ray Data on your system.
* Follow the provided code examples to implement the discussed problems.
* Experiment with different models and configurations for better performance.

## 2. Fundamental Level: Generating Responses to Common Questions

### 2.1. Problem

Imagine you have a list of common questions that people might ask a chatbot. Instead of generating a response for each question one by one, you want to generate answers for all the questions at once.

#### 2.2. Example Questions:

1. "What is the weather like today?"
2. "Tell me a joke."
3. "Give me a motivational quote."
4. "What is 2+2?"

### 2.3. Why Batch Processing?

Instead of sending each question to the chatbot separately, batch processing allows you to process all questions simultaneously, saving time and computing resources.

#### 2.4. Real-Life Example

Think about a customer support bot that receives thousands of similar questions daily. Instead of answering each one as it arrives, you could collect them and process them all at once, providing quick responses efficiently.

## 3. Intermediate Level: Text Generation from User Prompts

### 3.1. Problem

Now, imagine you have a list of user prompts for creative text generation. Instead of generating each text separately, you want to create a pipeline that processes all prompts together.

#### 3.2. Example Prompts:

* "Write a haiku about nature."
* "Generate a short story about space exploration."
* "Summarize the following paragraph..."

### 3.3. Why Use Ray Data LLM?

* You can use Ray Data to load a large number of prompts at once.
* Ray Data LLM allows you to batch process these prompts with the selected LLM model.

## 4. Advanced Level: Real-World Use Case

### 4.1. Problem

You have a dataset containing thousands of social media posts. You want to classify the sentiment (positive, negative, neutral) of each post using an LLM.

### 4.2. Why Batch Processing?

Performing sentiment analysis on each post individually would take too much time. By batching, you can classify the entire dataset at once.

#### 4.3. How Ray Data LLM Helps:

* Efficiently loads the dataset.
* Applies the LLM processor to generate sentiment labels.
* Uses distributed processing to handle large volumes of data efficiently.
