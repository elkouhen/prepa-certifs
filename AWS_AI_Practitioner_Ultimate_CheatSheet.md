# AWS AI Practitioner - Ultimate Cheat Sheet

# Part 0 - AI / ML / GenAI Fundamentals (Domain 1 & 2)

## AI vs ML vs Deep Learning vs GenAI vs Agentic AI

Definition: A hierarchy of nested concepts. AI (Artificial Intelligence) is the overall field; ML (Machine Learning) is a subset of AI where systems learn from data; Deep Learning is a subset of ML based on multi-layer neural networks; GenAI is a subset of Deep Learning capable of generating new content (text, image, audio...); Agentic AI adds a reasoning, planning, and autonomous action layer on top of a Foundation Model.

Key Points:
- AI ⊃ ML ⊃ Deep Learning ⊃ GenAI. Agentic AI = GenAI + ability to plan, act, and use tools autonomously.
- Deep Neural Network: Stacking multiple hidden layers of "neurons" (weights, biases + non-linear activation functions) that learn abstract representations of data.
- Computer Vision: AI discipline enabling systems to "understand" images and videos (**Amazon Rekognition**, **Amazon Textract**).
- NLP (Natural Language Processing): AI discipline that processes human language (**Amazon Comprehend**, **Amazon Lex**, **Amazon Translate**).
- Model vs Algorithm: The algorithm is the learning method (e.g., linear regression); the model is the trained output (learned weights) used for inference.
- Training vs Inferencing: Training = learning phase using historical data; inferencing = using the trained model to make predictions on new data.
- Bias / Fairness / Fit: 
  * Bias = systematic error; Fairness = absence of discrimination.
  * Underfitting = model is too simple, performs poorly on both training and test data.
  * Overfitting = model is too complex, memorizes training data, performs poorly on new test data (failed generalization).

Mnemonics: AI = the largest Russian doll, ML/DL/GenAI/Agentic = increasingly specific dolls inside.

🔥⭐⭐⭐⭐⭐

---

## ML Learning Types (Supervised / Unsupervised / Reinforcement)

Definition: Three main families of machine learning model training methods.

Key Points:
- Supervised Learning: The model learns from **labeled data** (input + known target output). Typical tasks: Classification (discrete categories) and Regression (continuous numerical values).
- Unsupervised Learning: The model learns patterns from **unlabeled data**. Typical tasks: Clustering (grouping similar items), Anomaly Detection, Dimensionality Reduction.
- Reinforcement Learning (RL): An agent learns by trial and error by interacting with an **environment**, taking **actions**, and maximizing a cumulative **reward**. 
- RLHF (Reinforcement Learning from Human Feedback): A critical post-training alignment method used to steer LLMs toward human preferences, reducing toxicity and hallucinations.

Technique Selection Examples:
- Regression: Predicting house prices, stock values, or temperatures (continuous values).
- Classification: Spam detection, customer churn prediction, or image categorization (discrete classes).
- Clustering: Customer segmentation by behavior without predefined groups.

Mnemonics: Supervised = with a teacher (labels); Unsupervised = without a teacher (hidden patterns); Reinforcement = trial and error with rewards.

🔥⭐⭐⭐⭐

---

## Data Types and Inference Types

Definition: Core concepts regarding data formats used in ML and execution modes for predictions.

Data Types:
- Labeled vs Unlabeled: Data with or without known answers/targets.
- Structured vs Unstructured: Tabular/relational databases vs free text, images, audio, and video.
- Tabular: Rows and columns (CSV, SQL tables).
- Time-series: Timestamped data points ordered sequentially over time.

Inference Modes (Conceptual Level):
- Real-time (Synchronous): Immediate response with low latency (e.g., e-commerce recommendation, chatbot).
- Streaming: A real-time subcategory vital for GenAI where tokens are returned one by one as they are generated.
- Asynchronous: Used for large or heavy workloads. Requests are placed in a queue, processed sequentially, and notifications are sent when complete (often via S3/SNS).
- Batch: High-volume processing executed in scheduled lots, completely decoupled from real-time constraints.

🔥⭐⭐⭐

---

## When to Use AI/ML (and when NOT to use it)

Definition: Identifying use cases where AI/ML adds clear business value versus scenarios where it is inappropriate.

When AI/ML Adds Value:
- Human decision support (recommendations, scoring, insights).
- Scaling repetitive tasks across massive data volumes.
- Automation of tasks that would be too costly or slow to perform manually.

When AI/ML is NOT Appropriate:
- When a **deterministic** and exact result is required instead of a probabilistic prediction (e.g., tax calculation, strict business rules -> use traditional code instead).
- When the cost-benefit analysis is unfavorable (training/maintenance cost > business value).
- When strict regulatory explainability is mandatory and a "black-box" model is unacceptable.

Trap: Traditional ML Model vs Foundation Model (FM)
- Traditional ML Model: Trained for ONE specific task, highly explainable, cheaper to run, well-suited for rigid regulatory constraints.
- FM: Generalist, multi-task capable via prompting, highly flexible but less explainable and potentially more expensive.

🔥⭐⭐⭐⭐

---

## AI/ML Lifecycle (Pipeline) and MLOps

Definition: The sequence of steps required to transform a business need into a production model, and the operations (MLOps) needed to maintain it reliably over time.

Typical Pipeline Steps:
1. Data Collection
2. Exploratory Data Analysis (EDA)
3. Data Preparation (Cleaning, feature engineering)
4. Model Training
5. Model Evaluation (Metrics)
6. Model Deployment (Inference)
7. Monitoring and Retraining

Model Performance Metrics:
- **Classification:** Accuracy (overall correctness), Precision (minimizing false positives), Recall (minimizing false negatives), F1-Score (harmonic mean of precision and recall).
- **Regression:** RMSE (Root Mean Squared Error), MAE (Mean Absolute Error).

MLOps Core Concepts:
- Reproducible experimentation, repeatable workflows, and scalable systems.
- Continuous monitoring of deployed models to detect performance decay over time.

Trap: Model Metric != Business Metric
- Precision, Recall, and F1-Score measure the statistical quality of the model.
- ROI, cost per transaction, and customer satisfaction score the actual business value.

🔥⭐⭐⭐⭐

---

## GenAI Core Concepts (Tokens, Embeddings, Transformers)

Definition: Fundamental vocabulary required to understand LLMs and generative applications.

Key Points:
- Token: Unit of text (words, sub-words, or characters) processed by the model. Amazon Bedrock billing is calculated primarily on tokens (input + output).
- Chunking: Breaking large documents into smaller segments before vectorization for RAG architectures.
- Embeddings: High-dimensional numerical vectors capturing semantic meaning. Synonym concepts will yield vectors with a very close distance (e.g., cosine similarity).
- Vector Database: Storage designed for embeddings to facilitate rapid semantic/similarity searches.
- Transformer: The core neural network architecture utilizing the **Self-Attention** mechanism, which underpins modern LLMs (GPT, Claude, Llama, Amazon Nova).
- Multimodal Model: A model capable of processing and generating multiple data modalities simultaneously (text + image + video).
- Diffusion Model: Architecture traditionally used for image generation (e.g., Stable Diffusion) by progressively removing noise from an image.
- Prompt Engineering: Formulating instructions given to a model to optimize output quality.

Mnemonics: Token = Lego brick, Embedding = GPS coordinates of meaning, Transformer = Engine of the LLM.

🔥⭐⭐⭐⭐⭐

---

## Foundation Model Lifecycle

Definition: The phases an FM undergoes from data assembly to production deployment.

Steps:
1. Data Selection (Assembling training data)
2. Model Selection (Choosing architecture and size)
3. Pre-training (Initial self-supervised learning on massive text corpora)
4. Fine-tuning (Downstream specialization using labeled datasets)
5. Evaluation (Quality and safety assessment)
6. Deployment (Exposing the model via APIs)
7. Feedback (Collecting interactions for iterative alignment)

🔥⭐⭐⭐

---

## Benefits and Limitations of GenAI

Definition: Balancing the high-impact business benefits of GenAI against its inherent constraints.

Benefits:
- Incredible adaptability (handles multiple distinct tasks via zero-shot/few-shot prompting).
- Natural language interface (highly intuitive conversational systems).
- Direct content generation (text, high-fidelity images, code synthesis).

Limitations / Risks:
- Hallucinations: Generating false or unverified statements presented confidently as facts.
- Interpretability: Extreme difficulty tracing exactly *why* a deep neural network generated a specific output.
- Non-determinism: Identical inputs can generate different outputs depending on inference parameters (temperature, top-p).

Cost Trade-Offs in FM Customization (From cheapest/simplest to costliest/most complex):
Prompt Engineering < Retrieval-Augmented Generation (RAG) < Fine-tuning < Continued Pre-training < Pre-training from scratch.

🔥⭐⭐⭐⭐⭐

---

# Part 1 - Amazon Bedrock

## Amazon Bedrock
Definition: A fully managed, serverless service providing access to industry-leading foundation models (FMs) via a unified API (Anthropic, Meta, Mistral AI, Cohere, Stability AI, and the proprietary Amazon Nova/Titan families).

Key Points:
- No infrastructure management (serverless), usage-based pricing per token.
- Enables easy switching and comparison of model endpoints (multi-LLM abstraction).
- Serves as the substrate for advanced GenAI features: Knowledge Bases, Agents, Guardrails, Fine-tuning.
- Security: Native isolation via IAM, KMS encryption, AWS PrivateLink (VPC), and full compliance tracking via CloudTrail.

Trap: Bedrock != SageMaker
- Bedrock = Utilizing pre-trained FMs via simple APIs without underlying resource orchestration.
- SageMaker = Building, training, and hosting customized or proprietary ML architectures from scratch with full infrastructure configuration.

🔥⭐⭐⭐⭐⭐

---

## Bedrock Knowledge Bases
Definition: A fully managed feature implementing end-to-end RAG (Retrieval-Augmented Generation). It automates document chunking, generates embeddings, stores them in a vector database, and fetches contextual snippets to ground the LLM's answers.

Step-by-Step Workflow:
1. Ingestion of data sources (e.g., files hosted on Amazon S3).
2. Chunking (Splitting files into logical text segments).
3. Embedding Generation (Vectorizing segments via an embedding model).
4. Vector Storage (Indexing embeddings into an isolated vector store).
5. Query Execution: User prompts -> Similarity search on vector store -> Context injected into prompt template -> Grounded response generated by LLM.

Supported Managed Vector Stores:
- **Amazon OpenSearch Serverless** (Vector Search Collection)
- **Amazon Aurora** (PostgreSQL with the `pgvector` extension)
- **Amazon RDS for PostgreSQL**
- **Amazon Neptune Analytics** (Specifically used for **GraphRAG** implementations)

Trap: Knowledge Bases != Bedrock Data Automation
- Knowledge Bases = Answering conversational queries using external documents (Semantic RAG search).
- Bedrock Data Automation = Converting raw, unstructured assets explicitly into structured JSON payloads.

🔥⭐⭐⭐⭐⭐

---

## Bedrock Data Automation (BDA)

Definition: A specialized multi-modal feature designed to ingest mass unstructured assets and transform them into precise structured outputs (JSON) without writing parsing pipelines.

Inputs:
- PDF (Invoices, complex contracts, forms).
- Images (Scans, layout designs, screenshots).
- Audio (Call logs, transcriptions).
- Video (Scene indexing, raw media assets).

Example:
A scanned invoice PDF -> Processed by BDA -> Structured JSON object `{"invoice_date": "2026-06-15", "amount_due": 1250.00}`.

🔥⭐⭐⭐⭐

---

## Bedrock Agents

Definition: Orchestration components capable of multi-step reasoning, automatic task planning, calling external APIs (via AWS Lambda), and querying Knowledge Bases to execute complex business workflows autonomously.

Components:
- Instructions (Defining persona, guardrails, and overarching goals).
- Action Groups (Set of REST APIs or Lambda functions the agent can execute).
- Knowledge Bases (Connected document repositories for factual lookup).
- Orchestrating Loop (The agent evaluates inputs, creates a task list, executes actions, tracks responses, and loops until completion).

🔥⭐⭐⭐⭐⭐

---

## Bedrock Guardrails

Definition: An independent evaluation layer applied to user prompts (inputs) and model responses (outputs) to filter unwanted content and enforce Responsible AI policies.

Capabilities:
- PII Redaction: Detecting and masking personally identifiable information (emails, SSNs, credit cards).
- Toxicity Filtering: Blocking hate speech, sexual content, violence, and dangerous inputs.
- Denied Topics: Explicitly setting custom filters for forbidden subjects (e.g., preventing a retail chatbot from giving medical advice).
- Grounding & Relevance Checks: Verifying that model outputs are strictly grounded in the RAG context to prevent hallucinations.

🔥⭐⭐⭐⭐⭐

---

## Bedrock Playground

Definition: A graphical user interface within the AWS Console used to rapidly test, evaluate, and experiment with text, chat, and image FMs. Users can tweak inference parameters (Temperature, Top-P, Max Tokens) without writing code.

🔥⭐⭐

---

## Bedrock Model Evaluation

Definition: A managed interface to score and compare different FMs (or different versions of the same fine-tuned model) against target quality baselines.

Two Evaluation Methods:
- Automatic Evaluation: Computes deterministic metrics (Accuracy, Robustness, Toxicity) using curated datasets.
- Human Evaluation: Utilizes internal teams or AWS-managed expert teams to score subjective traits (Brand Alignment, Nuance, Creativity).

🔥⭐⭐⭐⭐

---

## Bedrock Fine-tuning & Customization

Definition: Methods used to alter a model's intrinsic knowledge or task execution capabilities using proprietary data.

Key Modalities:
- Fine-tuning: Adapting a model using labeled data (prompt-response pairs) to execute specific downstream tasks.
- Continued Pre-training: Processing vast unlabeled domain text to infuse a base model (like Amazon Titan/Nova) with specialized industry terminology.
- Custom Model Import: Registering external open-source models (Llama, Mistral architectures) trained outside AWS to serve them securely via Bedrock APIs.
- Model Distillation: Training a smaller "student" model (e.g., Nova Micro) using outputs generated by a larger "teacher" model (e.g., Claude 3.5 Sonnet) to minimize inference latency and costs while keeping high quality.

Trap: Provisioned Throughput Allocation
- While standard models can be accessed via **On-Demand** pricing (pay-per-token), any customized model (Fine-tuned or Continued Pre-training) **must** be deployed using **Provisioned Throughput** (purchased capacity units billed hourly).

🔥⭐⭐⭐⭐

---

## Bedrock Marketplace

Definition: An expanded model catalog offering access to specialized, emerging, or niche foundation models beyond the native serverless models. These models are automatically deployed onto managed SageMaker endpoints behind the scenes but remain accessible via Bedrock workflows.

🔥⭐⭐

---

## Bedrock AgentCore

Definition: An enterprise-grade runtime platform built to orchestrate and scale autonomous GenAI agents with persistent operational scaffolding.

Core Modules:
- AgentCore Identity: Manages authentication, cryptographic keys, and granular IAM permissions assigned specifically to the agent's execution identity.
- Policy in AgentCore: Implements explicit operational guardrails and access control bounds for agent actions.
- AgentCore Memory: Separates ephemeral short-term session context from long-term memory structures to remember user preferences across distinct sessions.
- AgentCore Gateway: Serves as a secure egress layer for out-of-bounds API communication and web browsing requirements.

🔥⭐⭐⭐

---

## Prompt Engineering - Techniques and Vulnerabilities

Definition: Formulating and structuring text inputs to optimize the output of an FM without modifying weights.

Core Components:
- Context: Background or reference information.
- Instruction: The specific task requested.
- Negative Prompt: Explicit instructions detailing what the model must *not* include or generate (vital for image generation).

Advanced Techniques:
- Zero-shot: Prompting without providing examples.
- Few-shot: Including one or more sample inputs and expected outputs within the prompt to establish style or output schema constraints.
- Chain-of-Thought (CoT): Injecting instructions like "think step by step" to force the model to output its logical reasoning before presenting the final answer (improves math and logic performance).
- Bedrock Prompt Management: A centralized feature to create, version control, test, and deploy reusable prompt templates within applications.

Security Risks:
- Prompt Injection / Hijacking: An end-user crafts inputs that overwrite the system instructions, forcing the model to ignore its core safety rules.
- Prompt Poisoning: Injecting malicious data into training sets or RAG documentation sources to alter model assumptions.
- Jailbreaking: Using sophisticated adversarial prompts to bypass the safety and alignment filters of the model.

🔥⭐⭐⭐⭐⭐

---

## Agentic AI Protocols & Frameworks

Definition: Open specifications and software libraries used to scale agent-based systems and universal tool connectivity.

Key Terms:
- Model Context Protocol (MCP): An open-standard protocol enabling LLMs to securely connect to external tools, data sources, and APIs through an abstraction layer (similar to a "universal USB port" for AI agents).
- Multi-agent Patterns: System designs where distinct, specialized agents communicate (sequentially, hierarchically, or in parallel) to solve a broader problem.
- Strands Agents: An AWS open-source SDK framework used to construct and orchestrate programmatic agents (Code-First approach).
- Kiro: An agentic development IDE used to design, visualize, and generate applications via collaborative AI agents.

🔥⭐⭐⭐⭐

---

## Amazon Nova Familiy

Definition: Amazon's generation of multi-modal foundation models available via Amazon Bedrock.

Key Models:
- Text Capabilities: Scaling from ultra-fast/low-cost models (**Nova Micro**, **Nova Lite**) up to highly intelligent variants (**Nova Pro**, **Nova Premier**).
- Creative/Media Generation: **Nova Canvas** (image synthesis and editing) and **Nova Reel** (high-fidelity video generation).

🔥⭐⭐

---

# Part 2 - Amazon Q Assistants & Analytics

## Amazon Q Business

Definition: A fully managed, secure generative AI assistant for enterprise employees. It connects natively to internal data connectors (S3, SharePoint, Salesforce, Confluence) to answer business questions, summarize internal policies, and create reports while strictly respecting pre-existing user access permissions.

Key Point:
- Provides turnkey enterprise RAG out-of-the-box without requiring manual database or chunking configuration.

🔥⭐⭐⭐⭐⭐

## Amazon Q Developer

Definition: A generative AI assistant tailored for software engineers. It integrates into IDEs (VS Code, JetBrains) and the AWS Console to autocomplete code, explain complex algorithms, scan for security vulnerabilities, and debug infrastructure errors.

Key Feature:
- **Amazon Q Code Transformation** (AWS Transform): An automated workflow within Q Developer that modernizes legacy codebases automatically (e.g., upgrading Java 8 projects to Java 17, or transitioning .NET Framework to cross-platform .NET Core).

🔥⭐⭐⭐⭐⭐

## Amazon Q in QuickSight (Amazon Quick)

Definition: Generative BI (Business Intelligence) capabilities embedded within QuickSight. It allows data analysts and business stakeholders to discover insights, compile executive summaries, and generate interactive dashboards using natural language prompts.

🔥⭐⭐

---

# Part 3 - Amazon SageMaker

## SageMaker Canvas
Definition: A visual, no-code interface that enables business analysts and non-developers to build highly accurate machine learning models (Classification, Regression, Time-series forecasting) simply by uploading data tables.

🔥⭐⭐⭐⭐

## SageMaker Autopilot
Definition: An AutoML service that automates the entire ML pipeline: data cleaning, algorithm selection, hyperparameter tuning, and model ranking. It outputs transparent leaderboard tables alongside full execution notebooks so data scientists can inspect the generated code.

🔥⭐⭐⭐⭐

## SageMaker Data Wrangler
Definition: A visual data preparation tool within SageMaker Studio that simplifies data ingestion, selection, and transformation. It features hundreds of built-in code-free step options (handling missing inputs, one-hot encoding, normalization).

🔥⭐⭐⭐⭐

## SageMaker Feature Store
Definition: A managed repository to store, update, secure, and share ML features across data science teams. It maintains two structural components: an Online Store (low latency for real-time inference) and an Offline Store (high throughput storage in S3 for historical batch training).

🔥⭐⭐⭐

## SageMaker Training Jobs
Definition: A fully managed compute abstraction to train ML models at scale. It automatically provisions transient CPU/GPU infrastructure clusters, runs the training script, exports the model artifacts to S3, and tears down the infrastructure to minimize costs.

🔥⭐⭐⭐⭐

## SageMaker HyperParameter Tuning (AMT)
Definition: Also known as Automatic Model Tuning (AMT). It launches multiple parallel or sequential training jobs, dynamically varying hyperparameters (learning rates, layer counts) via optimization strategies (Bayesian, Random) to maximize a target validation metric.

🔥⭐⭐⭐

## SageMaker Experiments
Definition: A tracking registry that automatically logs configuration parameters, operational metrics, input data versions, and artifact locations for every training run to ensure experiment reproducibility.

🔥⭐⭐

## SageMaker Pipelines
Definition: A native CI/CD workflow engine tailored specifically for MLOps. It automates step dependencies from data ingestion, through training, model validation, registry approval, up to operational deployment.

🔥⭐⭐⭐

## SageMaker Model Registry
Definition: A centralized version-controlled repository to catalog trained ML models. It records performance metadata, legal lineage, and handles explicit lifecycle state transitions (Pending, Approved, Rejected) before production rollouts.

🔥⭐⭐⭐

## SageMaker Endpoints & Inference Options

Definition: Hosting mechanisms used to expose models for inferences depending on workload patterns.

Inference Modalities:
- Real-Time Inference: Persistent, always-on HTTPS endpoints optimized for low-latency millisecond responses under consistent traffic patterns.
- Asynchronous Inference: Built-in queuing (via Amazon SQS) for heavy workloads or huge payloads with execution windows up to 15 minutes. Can scale compute instances down to **zero** when the queue is empty.
- Serverless Inference: Managed endpoints built for intermittent or highly volatile traffic. Scales automatically from zero to meet instant request spikes, eliminating idle compute costs.
- Batch Transform: Non-persistent jobs designed to run inferences over large datasets stored in S3. It spins up compute resources, appends batch predictions, writes outputs to S3, and immediately shuts down.

🔥⭐⭐⭐⭐

## SageMaker Clarify
Definition: A comprehensive assessment tool used to evaluate dataset and model behavior. It detects statistical **bias** (both pre-training in datasets and post-training in predictions) and provides **explainability** insights by calculating feature importance metrics (SHAP values).

> 💡 **Examen Tip:** Clarify is primarily utilized **during data prep, training, and pre-deployment validation** phases.

🔥⭐⭐⭐⭐⭐

## SageMaker Model Monitor
Definition: A continuous validation service designed to audit the performance of real-time endpoints **live in production**. It monitors incoming payloads and alerts engineers (via CloudWatch) to architectural **Drift**:
- Data Drift: Changing real-world input distributions relative to training data.
- Concept Drift: Structural decay in model prediction accuracy over time.

> 💡 **Examen Tip:** Model Monitor operates **exclusively post-deployment on production endpoints**.

🔥⭐⭐⭐⭐

## SageMaker Ground Truth
Definition: A managed data labeling service that coordinates distributed human workforces (Internal teams, Amazon Mechanical Turk, or third-party vendors) alongside active learning ML models to label raw datasets.

🔥⭐⭐⭐

## SageMaker JumpStart
Definition: An enterprise hub offering one-click access to hundreds of pre-trained open-source algorithms, pre-built solution templates, and popular foundation models that can be directly deployed onto managed SageMaker infrastructure.

Trap: JumpStart != Bedrock
- JumpStart = Deploying models explicitly onto hosted SageMaker compute infrastructure (giving full container and instance control, ideal for data science teams).
- Bedrock = Accessing fully serverless FMs via consumption-based APIs without provisioning or configuring infrastructure.

🔥⭐⭐⭐⭐⭐

## SageMaker Studio
Definition: A unified web-based Integrated Development Environment (IDE) that aggregates all SageMaker sub-services (JupyterLab, Code Editor, Canvas, Pipelines, Lineage Tracking) into collaborative shared spaces.

🔥⭐⭐⭐ 