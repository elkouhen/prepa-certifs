# AWS AI Practitioner - Ultimate Cheat Sheet

## How This Cheat Sheet Is Built (Methodology)

- **Rule 1 - Scope:** Each section covers one AWS service (or a key feature of Amazon Bedrock / Amazon SageMaker) that has a dedicated official AWS FAQ page, listed in **Reference FAQs** at the end of this document.
- **Rule 2 - Order:** Services are ordered to follow a typical AI/ML pipeline: **Part 1** (pre-built AI services that consume data) -> **Part 2** (Generative AI via Amazon Bedrock) -> **Part 3** (custom ML via Amazon SageMaker, from data prep to monitoring).
- **Rule 3 - Entry structure:** Each entry must strictly follow this exam-focused structure:
  - **Definition & Purpose:** What the service is and why you would use it (must explicitly mention standard AWS ecosystem connectors and integrations).
  - **Exam Triggers:** The exact keywords, phrases, or constraints in an exam question that point directly to this service.
  - **Not to be confused with:** (MANDATORY) The closest competing AWS service or feature, with the exact decision criteria used to choose between them (e.g., Cost, Code vs. No-Code, Serverless vs. Infrastructure control).
  - **Security & FAQ Insights:** Critical security patterns (e.g., data isolation, KMS encryption, VPC endpoints) and specific limitations directly extracted from the official AWS FAQ.
  - **🔥⭐ rating:** Estimated exam importance, from 1 to 5 stars.

---

# Part 1 - AWS Managed AI Services (Pre-built AI)

## Amazon Textract

**Definition & Purpose:** An OCR and document-understanding service that extracts text, tables, forms, and structured key-value data from scanned documents and images. Use it to turn unstructured documents (PDFs, scans, photos) into structured, queryable data without building or training a custom OCR model. Standard integrations: **S3** (input documents and output JSON), **SNS/SQS** (async job completion notifications for large multi-page documents), **Lambda** (orchestrating extraction pipelines), and **Amazon A2I** (routing low-confidence extractions to human reviewers).

**Exam Triggers:** "extract text/tables/forms from scanned documents or images", "automate invoice/receipt processing", "extract data from IDs/passports/driver's licenses", "OCR at scale".

**Not to be confused with:** Amazon Comprehend - Textract performs OCR/extraction (turning a document image into raw text and structured fields), while Comprehend analyzes text that already exists (sentiment, entities, PII) to extract meaning. Decision criteria: input is an **image/scanned document** -> Textract; input is **plain text** -> Comprehend.

**Security & FAQ Insights:** Analyze Document (text/forms/tables), Analyze Expense (receipts/invoices: vendor, total, line items), and Analyze ID (driver's licenses/passports) are distinct specialized APIs. Output can be encrypted with KMS; supports VPC endpoints for private access; Amazon A2I provides a managed human-review loop for low-confidence results.

🔥⭐⭐⭐⭐

---

## Amazon Comprehend

**Definition & Purpose:** An NLP service that uses ML to extract insights from text, including sentiment, entities, key phrases, language, syntax, and PII - without training a custom NLP model. Standard integrations: **S3** (batch analysis jobs), **Lambda** (event-driven analysis), **Kinesis Data Firehose** (inline PII detection/redaction on streaming data), and **Textract** (analyzing text extracted from documents).

**Exam Triggers:** "detect sentiment in customer feedback/emails", "identify and redact PII in text", "extract entities or key phrases from unstructured text", "classify documents into custom categories".

**Not to be confused with:** Amazon Textract - Comprehend analyzes text that is already in digital form, while Textract is required first if the source is a scanned document or image. Also distinct from **Comprehend Medical**, a specialized variant for clinical text (conditions, medications, dosages).

**Security & FAQ Insights:** Comprehend Custom allows training custom classifiers and entity recognizers on your own labeled data. PII detection/redaction can run directly inline within a Kinesis Data Firehose delivery stream. Supports VPC endpoints and KMS encryption for custom model artifacts and analysis jobs.

🔥⭐⭐⭐⭐

---

## Amazon Transcribe

**Definition & Purpose:** An automatic speech recognition (ASR) service that converts audio and video into text, supporting both real-time streaming and asynchronous batch jobs. Standard integrations: **S3** (batch audio input/output), **Kinesis Video/Audio Streams** (real-time transcription), **Amazon Connect** (call-center transcription), and **Amazon Comprehend** (sentiment/entity analysis on the resulting transcript).

**Exam Triggers:** "transcribe call-center recordings", "generate subtitles/captions", "redact PII (e.g., credit card numbers) from spoken audio", "convert speech to text in real time".

**Not to be confused with:** Amazon Translate - Translate converts text between written languages, it does not process audio. Also distinct from **Amazon Polly**, which performs the reverse operation (text-to-speech). Decision criteria: source is **audio** -> Transcribe; source is **text needing language conversion** -> Translate; need to **generate speech** -> Polly.

**Security & FAQ Insights:** Custom vocabularies and language models improve accuracy on domain-specific terms (product names, jargon). Automatic PII identification/redaction is built in. **Transcribe Medical** is a specialized variant for clinical documentation. Supports VPC endpoints for private API access.

🔥⭐⭐⭐

---

## Amazon Translate

**Definition & Purpose:** A neural machine translation service that translates text between languages in real time or in batch, enabling localized and multilingual applications without managing translation models. Standard integrations: **S3** (batch document translation jobs), **Amazon Comprehend** (automatic source-language detection), and application-layer services (CloudFront, Lambda) for on-the-fly content localization.

**Exam Triggers:** "translate text or documents between languages", "build a multilingual application", "improve translation quality for domain-specific terminology without training a custom model".

**Not to be confused with:** Amazon Transcribe - Translate works on written text, never on audio. Decision criteria for customization: need higher-quality translations for a specific domain using your own parallel data **without** a full custom model -> **Active Custom Translation (ACT)**; need full model training -> not supported by Translate (would require SageMaker).

**Security & FAQ Insights:** Active Custom Translation (ACT) improves domain-specific translation quality using your own parallel data, without training or managing a full custom model. Batch translation jobs and stored data can be encrypted via KMS.

🔥⭐⭐⭐

---

## Amazon Lex

**Definition & Purpose:** A service for building conversational interfaces (chatbots and voice bots) using the same deep learning technology as Alexa, allowing teams to build dialogue management and intent recognition without building NLU from scratch. Standard integrations: **Amazon Connect** (contact-center voice bots), **AWS Lambda** (executing business logic/fulfillment for intents), and **CloudWatch** (conversation logging).

**Exam Triggers:** "build a chatbot or voice bot", "manage dialogue with intents, utterances, and slots", "deflect contact-center calls with a virtual agent".

**Not to be confused with:** Amazon Q Business / Bedrock Agents - Lex manages structured, intent/slot-based conversational flows for well-defined tasks (check balance, book appointment), whereas Q Business and Bedrock Agents provide open-ended generative reasoning over broad knowledge sources. Decision criteria: **fixed set of known intents/tasks** -> Lex; **open-ended Q&A or multi-step autonomous reasoning** -> Q Business/Bedrock Agents.

**Security & FAQ Insights:** Bots are defined using intents, sample utterances, and slots (the information needed to fulfill an intent). Conversation logs can be encrypted with KMS and sent to CloudWatch/S3. Integrates with Amazon Cognito for end-user authentication within bots.

🔥⭐⭐⭐

---

## Amazon Kendra

**Definition & Purpose:** An intelligent enterprise search service powered by ML that answers natural-language questions across multiple connected data repositories using semantic search rather than keyword matching. Standard integrations: **40+ built-in connectors** (S3, SharePoint, Salesforce, Confluence, etc.) and as a retrieval backend for **Amazon Q Business**.

**Exam Triggers:** "natural-language search/Q&A across internal documents", "enterprise search spanning multiple data silos", "employees ask questions and get precise answers from internal documents".

**Not to be confused with:** Amazon Q Business - Q Business is a full generative AI assistant that synthesizes conversational answers (and can use Kendra as a retriever underneath), whereas Kendra itself is a search/retrieval engine that returns relevant documents/passages. Decision criteria: need a **generative conversational assistant** -> Q Business; need a **semantic search/retrieval index** -> Kendra.

**Security & FAQ Insights:** Per-document Access Control Lists (ACLs) reflect the permissions of the source repository, ensuring users only see results they're authorized to access. Supports KMS encryption and VPC deployment for private indexing/search.

🔥⭐⭐⭐

---

## Amazon Personalize

**Definition & Purpose:** A managed service that builds real-time recommendation and personalization models using the same ML technology as Amazon.com, without requiring in-house ML expertise. Standard integrations: **S3** (historical training data), **Kinesis** (real-time interaction event streams), and **Amazon Pinpoint** (delivering personalized campaigns based on recommendations).

**Exam Triggers:** "show personalized product/content recommendations", "next-best-action recommendations", "real-time personalization based on user behavior".

**Not to be confused with:** Amazon Forecast - Forecast predicts future numeric values (demand, inventory) over time, while Personalize generates individualized recommendations per user. Decision criteria: predicting a **quantity over time** -> Forecast (now SageMaker Canvas); recommending **items to a specific user** -> Personalize.

**Security & FAQ Insights:** Provides pre-built "recipes" for common use cases (product recommendations, related items, personalized re-ranking, next-best-action). Recommendations can be returned in real time via API or generated in batch. Data is encrypted at rest via KMS, and training requires a minimum volume of historical interaction data.

🔥⭐⭐⭐

---

## Amazon Forecast

**Definition & Purpose:** A managed time-series forecasting service that uses ML to predict future values (demand, inventory, resource needs) from historical data, without building and maintaining custom time-series models. Standard integration: **S3** (historical data input).

**Exam Triggers:** "predict future demand/inventory from historical time-series data" - but watch for the exam trap below, which may point the correct answer to SageMaker Canvas instead.

**Not to be confused with:** SageMaker Canvas - Amazon Forecast is **no longer onboarding new customers**; AWS positions SageMaker Canvas as its no-code successor for time-series forecasting use cases. Decision criteria: for a **new** project requiring no-code forecasting, the exam-correct answer is SageMaker Canvas, not Forecast.

**Security & FAQ Insights:** Exam trap - existing customers can continue using Forecast, but its forecasting capabilities are being consolidated into Amazon SageMaker Canvas, which is the forward-looking answer on the exam.

🔥⭐⭐

---

# Part 2 - Amazon Bedrock (Generative AI)

## Amazon Bedrock

**Definition & Purpose:** A fully managed, serverless service providing access to industry-leading foundation models (FMs) via a unified API (Anthropic, Meta, Mistral AI, Cohere, Stability AI, and the proprietary Amazon Nova/Titan families), without managing any underlying infrastructure. Standard integrations: **IAM** (access control), **KMS** (encryption), **AWS PrivateLink** (private VPC access), **CloudTrail** (API auditability), **Bedrock Knowledge Bases**, and **Bedrock Agents**.

**Exam Triggers:** "access multiple foundation models through a single API without managing infrastructure", "compare/test models from different providers", "serverless generative AI".

**Not to be confused with:** Amazon SageMaker - SageMaker is for building, training, and hosting **custom** models where the customer manages the underlying infrastructure (instances, clusters), while Bedrock provides serverless API access to **pre-trained** FMs. Decision criteria: need **infrastructure control / custom model training** -> SageMaker; need **quick, serverless access to existing FMs** -> Bedrock.

**Security & FAQ Insights:** Two pricing models - On-Demand (pay per token, no commitment) and Provisioned Throughput (purchased capacity units billed hourly, required for customized/fine-tuned models). AWS does not use customer prompts/outputs to train the underlying base models (data isolation). PrivateLink enables VPC-only access without traversing the public internet; CloudTrail logs every API call for auditability; KMS encrypts custom model artifacts.

🔥⭐⭐⭐⭐⭐

---

## Bedrock Knowledge Bases

**Definition & Purpose:** A fully managed feature implementing end-to-end RAG (Retrieval-Augmented Generation): it automates document chunking, generates embeddings, stores them in a vector database, and fetches contextual snippets to ground an LLM's answers in your own data. Standard integrations: **S3** (source documents), supported vector stores - **Amazon OpenSearch Serverless**, **Amazon Aurora (pgvector)**, **Amazon RDS for PostgreSQL**, and **Amazon Neptune Analytics** (for GraphRAG) - and **Bedrock Agents** (as a tool for retrieval).

**Exam Triggers:** "ground LLM answers in company documents", "implement RAG without managing an embeddings pipeline", "internal assistant answering questions from S3-stored policies".

**Not to be confused with:** Amazon Kendra - Kendra is a general-purpose search/retrieval engine returning ranked documents to a user or application, while Knowledge Bases is purpose-built to retrieve chunks and inject them directly into an LLM prompt for generation. Also distinct from **Bedrock Agents**, which can use a Knowledge Base as one of several tools in a broader multi-step workflow. Decision criteria: need **retrieved context fed into an LLM response** -> Knowledge Bases; need **document search results for users** -> Kendra.

**Security & FAQ Insights:** Workflow: ingest from S3 -> chunk documents -> generate embeddings -> store in chosen vector database -> retrieve relevant chunks at query time. Data remains within the customer's selected vector store and can be encrypted via KMS; ingestion jobs sync incrementally from the S3 source.

🔥⭐⭐⭐⭐⭐

---

## Bedrock Agents

**Definition & Purpose:** Orchestration components capable of multi-step reasoning, automatic task planning, calling external APIs (via AWS Lambda), and querying Knowledge Bases to execute complex business workflows autonomously. Standard integrations: **AWS Lambda** (Action Groups - the APIs/functions the agent can call), **Bedrock Knowledge Bases** (factual lookup), and **Bedrock Guardrails** (policy enforcement on agent input/output).

**Exam Triggers:** "automate a complex multi-step business workflow", "agent that plans tasks, calls APIs, and acts autonomously", "orchestrate reasoning across multiple tools".

**Not to be confused with:** Bedrock Knowledge Bases - Knowledge Bases provide passive retrieval to ground a single response, while Agents actively plan a sequence of steps, execute them (including calling APIs), observe results, and loop until the task is complete. Decision criteria: need **multi-step autonomous execution / API calls** -> Agents; need **only retrieval to ground an answer** -> Knowledge Bases.

**Security & FAQ Insights:** Core components: Instructions (persona/goals), Action Groups (APIs/Lambda functions defined via OpenAPI schemas, executed with scoped IAM permissions), and optional Knowledge Bases. Bedrock Guardrails can be applied to both agent inputs and outputs; agents support session-based memory across a conversation.

🔥⭐⭐⭐⭐⭐

---

## Bedrock Guardrails

**Definition & Purpose:** An independent evaluation layer applied to user prompts (inputs) and model responses (outputs) to filter unwanted content and enforce Responsible AI policies. Standard integrations: applies to any **Bedrock model invocation**, **Bedrock Agents**, and **Bedrock Knowledge Bases** (via grounding/relevance checks on RAG responses).

**Exam Triggers:** "prevent a chatbot from discussing certain topics", "redact PII from model responses", "enforce Responsible AI / content-safety policy on a generative AI application".

**Not to be confused with:** Amazon Comprehend (PII detection) - Comprehend is a general-purpose text-analysis service usable anywhere, while Guardrails is purpose-built and natively wired into the Bedrock model-invocation pipeline (including denied topics, content filters, and RAG grounding checks). Decision criteria: need **policy enforcement integrated directly into a Bedrock model/agent call** -> Guardrails; need **standalone PII detection on arbitrary text** -> Comprehend.

**Security & FAQ Insights:** Configurable policy types: denied topics, content filters (hate, violence, etc.), PII redaction/masking, custom word filters, and grounding/relevance checks for RAG outputs. Applied independently to both the prompt (input) and the model's response (output), and can be reused across multiple models and agents.

🔥⭐⭐⭐⭐⭐

---

## Bedrock Fine-tuning

**Definition & Purpose:** A customization method that adapts a foundation model using **labeled prompt-response pairs** for a specific task, improving accuracy and consistency on that task. Standard integrations: **S3** (training data storage), **KMS** (encryption of training artifacts).

**Exam Triggers:** "improve a model's accuracy/consistency on a specific task using your own labeled examples", "force a model to always respond in a specific format".

**Not to be confused with:** Bedrock Continued Pre-training - Continued Pre-training uses large volumes of **unlabeled** domain text to inject broad domain knowledge, while Fine-tuning uses **labeled** prompt-response pairs to shape behavior for a specific task. Decision criteria: have **labeled examples of desired input/output** -> Fine-tuning; have **large unlabeled domain corpus** -> Continued Pre-training.

**Security & FAQ Insights:** Exam trap - any fine-tuned model **must** be served via Provisioned Throughput, never On-Demand. Training data is stored encrypted in S3 via KMS, and the resulting fine-tuned model weights remain private to the customer's account.

🔥⭐⭐⭐⭐

---

## Bedrock Continued Pre-training

**Definition & Purpose:** A customization method that further trains a base foundation model on large volumes of **unlabeled** domain-specific text, injecting deep domain knowledge (vocabulary, style, facts) before any task-specific fine-tuning. Standard integration: **S3** (large unlabeled corpus storage).

**Exam Triggers:** "inject domain-specific vocabulary/knowledge into a model before fine-tuning", "continue pre-training on internal technical documentation".

**Not to be confused with:** Bedrock Fine-tuning - Fine-tuning requires labeled prompt-response pairs for a specific task, while Continued Pre-training uses large amounts of unlabeled data for broad knowledge injection and is often performed **before** fine-tuning.

**Security & FAQ Insights:** Exam trap - a continued pre-trained model must also be served via Provisioned Throughput, not On-Demand. The resulting model remains private to the customer's account.

🔥⭐⭐⭐

---

## Bedrock Custom Model Import

**Definition & Purpose:** A feature that brings externally trained open-source models (e.g., Llama, Mistral) into Bedrock to be served through its unified, serverless API, with no infrastructure to manage. Standard integration: works alongside native Bedrock models within the same unified API, billing, and scaling model.

**Exam Triggers:** "run your own or a third-party open-source model within Bedrock's serverless environment", "import a custom fine-tuned Llama/Mistral model".

**Not to be confused with:** SageMaker JumpStart - JumpStart deploys open-source models to a **self-managed SageMaker endpoint** (customer controls infrastructure), whereas Custom Model Import serves them through **Bedrock's serverless** environment. Decision criteria: want **serverless, Bedrock-native serving** -> Custom Model Import; want **full endpoint/infrastructure control** -> JumpStart.

**Security & FAQ Insights:** Only supports specific open-source architectures (e.g., Llama, Mistral, Mixtral, Flan-T5) - not arbitrary custom architectures. Imported models are billed and scaled exactly like other Bedrock models.

🔥⭐⭐⭐

---

## Bedrock Model Distillation

**Definition & Purpose:** A customization method that trains a smaller "student" model to replicate the behavior of a larger "teacher" model, using the teacher's outputs as training data, to reduce inference latency and cost while retaining much of the teacher's quality.

**Exam Triggers:** "reduce inference latency/cost for a high-volume application while retaining quality", "distill a large model into a smaller one".

**Not to be confused with:** Bedrock Fine-tuning - Fine-tuning adapts a model to a task using **your own labeled data**, whereas Distillation transfers the **behavior of an existing larger model** into a smaller model using the teacher's generated responses as training data.

**Security & FAQ Insights:** The teacher model's responses to a set of prompts become the training data for the student model. Trades some accuracy for significantly lower latency and cost compared to the teacher.

🔥⭐⭐⭐

---

## Bedrock Model Evaluation

**Definition & Purpose:** A managed interface to score and compare different FMs (or different versions of the same fine-tuned model) against target quality baselines, and more broadly to measure whether an FM-powered application reliably solves the business problem. Standard integration: works with any model accessible via Bedrock, using built-in or custom datasets.

**Exam Triggers:** "objectively compare candidate foundation models", "measure whether an FM application meets the business requirement", "ROUGE/BLEU/BERTScore/LLM-as-a-judge".

**Not to be confused with:** SageMaker Clarify - Clarify evaluates **bias and explainability** for custom ML models trained in SageMaker, while Bedrock Model Evaluation benchmarks the **quality/output** of foundation models and GenAI applications. Decision criteria: evaluating **FM output quality/business fit** -> Bedrock Model Evaluation; evaluating **bias/fairness of a trained ML model** -> SageMaker Clarify.

**Security & FAQ Insights:** Automatic evaluation computes metrics such as accuracy, robustness, and toxicity using built-in or custom datasets. Human evaluation uses internal teams or AWS-managed workers to score subjective qualities (tone, brand alignment, creativity). Common metrics: ROUGE and BLEU (summarization/translation overlap), BERTScore (semantic similarity), and LLM-as-a-judge.

🔥⭐⭐⭐⭐

---

## Amazon Nova Family

**Definition & Purpose:** Amazon's family of multimodal foundation models available through Amazon Bedrock, providing a range of proprietary FMs covering text and media generation at different cost/performance levels. Standard integration: accessed like any other model through the **Bedrock unified API**, with the same IAM/KMS/PrivateLink security model.

**Exam Triggers:** "lowest-cost Amazon model for a high-volume, low-cost use case" -> Nova Micro; "most capable Amazon model for complex tasks" -> Nova Premier; "Amazon model for image generation/editing" -> Nova Canvas; "Amazon model for video generation" -> Nova Reel.

**Not to be confused with:** Amazon Titan - Titan is Amazon's older proprietary Bedrock model family, with Nova positioned as its successor across text and media generation tiers.

**Security & FAQ Insights:** Text models scale from Nova Micro (fastest/cheapest) to Nova Lite, Nova Pro, and Nova Premier (most capable). Creative/media models: Nova Canvas (image generation/editing) and Nova Reel (video generation). Inherits all standard Bedrock security controls (IAM, KMS, PrivateLink, CloudTrail).

🔥⭐⭐

---

# Part 3 - Amazon SageMaker (Custom ML)

## Amazon SageMaker

**Definition & Purpose:** A fully managed service that provides the tools to build, train, and deploy custom machine learning models across every stage of the ML lifecycle, from data preparation to production monitoring, giving teams full control over building and operating custom ML models. Standard integrations: **S3** (data/artifacts), **AWS Glue/Athena** (data preparation and querying), **ECR** (custom training/inference containers), and **VPC** (network isolation for training/hosting).

**Exam Triggers:** "train a custom model on proprietary/transaction data", "need full control over training/hosting infrastructure", "build, train, tune, deploy, and monitor a custom fraud-detection model".

**Not to be confused with:** Amazon Bedrock - Bedrock provides serverless API consumption of **pre-trained** FMs with no infrastructure to manage, while SageMaker requires choosing and configuring the underlying compute (instance types, clusters) to build **custom** models. Decision criteria: need a **pre-trained FM via API** -> Bedrock; need a **custom model trained on your own data with infra control** -> SageMaker.

**Security & FAQ Insights:** Covers the full ML lifecycle: data preparation, build, train, tune, deploy, and monitor. Supports VPC-only training/hosting (no internet access), KMS encryption of data and model artifacts, and per-job/per-notebook IAM execution roles.

🔥⭐⭐⭐⭐⭐

---

## SageMaker Unified Studio

**Definition & Purpose:** A single, web-based interface that unifies SageMaker's ML tools with data engineering, data analytics (Athena, Redshift, EMR, Glue), and generative AI capabilities into one collaborative workspace, governed through Amazon DataZone. Standard integrations: **Amazon DataZone** (project-based governance/access control), and previously separate consoles (SageMaker, Glue, Athena, Redshift, EMR).

**Exam Triggers:** "single entry point for data and ML teams to discover data, build pipelines, and train models", "unified governance across multiple analytics and ML consoles".

**Not to be confused with:** SageMaker Studio - Studio is the ML-focused IDE (notebooks, code editor, pipelines), which is now one of the tools accessible **from within** Unified Studio's broader, project-based workspace. Decision criteria: need **cross-discipline data + ML + analytics workspace with governance** -> Unified Studio; need **ML-specific IDE only** -> Studio.

**Security & FAQ Insights:** Governance, access control, and cataloging across projects are managed through Amazon DataZone, providing a project-based permission model spanning previously siloed consoles.

🔥⭐⭐

---

## SageMaker Studio

**Definition & Purpose:** A unified, web-based ML IDE that integrates Jupyter notebooks, a code editor, pipelines, and experiment tracking in a single interface, giving data scientists one environment to build, train, debug, and deploy custom ML models. Standard integration: **SageMaker Pipelines** (CI/CD-style ML workflows).

**Exam Triggers:** "single IDE for data scientists to write code, run experiments, and track results", "decouple notebook compute from storage for fast instance switching".

**Not to be confused with:** SageMaker Unified Studio - Unified Studio is the broader, project-based workspace that wraps Studio together with data engineering and analytics tools; Studio itself remains the ML-specific IDE. Decision criteria: question focuses purely on **ML development experience** -> Studio; question involves **cross-team data/analytics collaboration** -> Unified Studio.

**Security & FAQ Insights:** Studio Notebooks decouple compute from storage, allowing fast instance switching without losing notebook data. Integrates with SageMaker Pipelines for CI/CD-style ML workflows.

🔥⭐⭐⭐

---

## SageMaker Canvas

**Definition & Purpose:** A no-code, visual interface that lets business analysts build ML models for classification, regression, and time-series forecasting directly from tabular data, without writing any code. Standard integration: **SageMaker Model Registry** (sharing models built in Canvas with data science teams for further validation).

**Exam Triggers:** "business analyst builds an ML model without writing code", "no-code time-series forecasting from a spreadsheet of historical data".

**Not to be confused with:** Amazon Forecast - Forecast is no longer onboarding new customers, and AWS positions SageMaker Canvas as its **no-code successor** for time-series forecasting use cases. Decision criteria: any **new** no-code forecasting requirement on the exam should point to Canvas, not Forecast.

**Security & FAQ Insights:** Automatically tests multiple algorithms and selects the best-performing one for the dataset. Models built in Canvas can be registered in SageMaker Model Registry for collaboration with data science teams.

🔥⭐⭐⭐

---

## SageMaker JumpStart

**Definition & Purpose:** A model hub providing one-click access to pre-trained open-source foundation models and solution templates that can be fine-tuned and deployed to **customer-managed SageMaker endpoints**, accelerating ML development by starting from pre-trained models rather than from scratch. Standard integration: deploys to **SageMaker endpoints** within the customer's VPC.

**Exam Triggers:** "deploy a pre-trained open-source model to your own endpoint and fine-tune it", "one-click access to open-source NLP/vision/tabular model templates".

**Not to be confused with:** Bedrock Custom Model Import - Custom Model Import serves an open-source model through Bedrock's **serverless** environment, while JumpStart deploys it to a **self-managed SageMaker endpoint** where the customer controls infrastructure. Decision criteria: want **infrastructure control over the deployed model** -> JumpStart; want **serverless Bedrock-native serving** -> Custom Model Import.

**Security & FAQ Insights:** Models are deployed within the customer's VPC/endpoint with full infrastructure control (instance types, scaling). Covers NLP, vision, and tabular model templates, not just generative AI.

🔥⭐⭐⭐

---

## SageMaker Ground Truth

**Definition & Purpose:** A data labeling service that combines human labelers (in-house teams, Amazon Mechanical Turk, or third-party vendors) with active learning to efficiently generate high-quality labeled datasets at scale.

**Exam Triggers:** "label thousands of images/records for training", "combine automated labeling with human review to reduce cost".

**Not to be confused with:** SageMaker Clarify - Ground Truth **creates** labeled training data, whereas Clarify **analyzes** existing labeled data and model predictions for bias and explainability. Decision criteria: need to **produce labels** -> Ground Truth; need to **audit labels/predictions for bias** -> Clarify.

**Security & FAQ Insights:** Active learning automatically labels "easy" examples and routes only ambiguous ones to human reviewers (in-house teams, Mechanical Turk, or vendors), reducing labeling cost and time.

🔥⭐⭐⭐

---

## SageMaker Data Wrangler

**Definition & Purpose:** A visual data preparation tool offering hundreds of built-in, no-code transformations (imputation, encoding, normalization) for cleaning and feature engineering before training. Standard integrations: **SageMaker Feature Store** and **SageMaker Pipelines** (to operationalize and reuse transformations).

**Exam Triggers:** "quickly clean, transform, and visualize data before training without custom code", "normalize numerical features and one-hot encode categorical columns".

**Not to be confused with:** SageMaker Feature Store - Data Wrangler is the **upstream** tool used to create and apply transformations, while Feature Store is the **downstream** repository that stores and serves the resulting features for reuse. Decision criteria: question is about **preparing/transforming raw data** -> Data Wrangler; question is about **storing/serving features consistently** -> Feature Store.

**Security & FAQ Insights:** Exported transformation flows integrate directly with Feature Store and SageMaker Pipelines, allowing transformations to be operationalized and reused across training and inference.

🔥⭐⭐

---

## SageMaker Feature Store

**Definition & Purpose:** A centralized repository for storing, sharing, and serving ML features, with an **Online Store** (low-latency real-time lookups) and an **Offline Store** (S3-based, for training and batch use), ensuring consistency of features between training and inference. Standard integration: **S3** (Offline Store, integrates with data lakes and analytics tools).

**Exam Triggers:** "ensure consistency of features between training and inference", "avoid training/serving skew", "retrieve the latest customer features at inference time while reusing the same features historically for training".

**Not to be confused with:** SageMaker Data Wrangler - Data Wrangler creates/transforms features upstream, while Feature Store stores and serves the finalized features for both training (Offline Store) and real-time inference (Online Store).

**Security & FAQ Insights:** Prevents training/serving skew by ensuring the same feature definitions are used for both training and inference. The Offline Store is backed by S3 and integrates with data lakes and analytics tools; the Online Store provides low-latency lookups for real-time inference.

🔥⭐⭐⭐

---

## SageMaker Training Jobs

**Definition & Purpose:** A managed mechanism that provisions compute, runs a training script on a chosen dataset, writes the resulting model artifacts to S3, and tears down the infrastructure afterward, allowing custom models to be trained on managed, on-demand infrastructure without managing servers. Standard integrations: **S3** (data and artifacts), **ECR** (Bring-Your-Own-Container), and built-in frameworks (PyTorch, TensorFlow, etc.).

**Exam Triggers:** "train on managed, on-demand infrastructure and pay only for training duration", "submit a training job that spins up GPU instances and stops automatically".

**Not to be confused with:** SageMaker HyperPod - Training Jobs provision ephemeral compute for a typical single training job, while HyperPod provides a **persistent, self-healing cluster** purpose-built for very large-scale, long-running distributed training. Decision criteria: **typical, single training job** -> Training Jobs; **multi-week, multi-billion-parameter distributed training with failure recovery** -> HyperPod.

**Security & FAQ Insights:** You pay only for the training duration; supports built-in algorithms, custom containers (BYOC via ECR), and popular frameworks (PyTorch, TensorFlow, etc.). Can run within a VPC with no internet access.

🔥⭐⭐⭐⭐

---

## SageMaker HyperPod

**Definition & Purpose:** A purpose-built, persistent and self-healing compute cluster service for large-scale distributed training of foundation models, reducing training time and operational overhead for very large models trained over weeks or months.

**Exam Triggers:** "train a multi-billion-parameter foundation model across hundreds of GPUs with automatic failure recovery", "minimize lost training time from hardware failures during long-running training".

**Not to be confused with:** SageMaker Training Jobs - Training Jobs are ephemeral, single-job compute that tears down after completion, while HyperPod maintains a **persistent** cluster that automatically detects, repairs, and resumes from the last checkpoint after hardware failures.

**Security & FAQ Insights:** Automatically detects, repairs, and resumes training from the last checkpoint after hardware failures, minimizing lost training time. Targeted at large-scale, long-running distributed training jobs rather than typical single-job training.

🔥⭐⭐

---

## SageMaker Automatic Model Tuning (AMT)

**Definition & Purpose:** A managed hyperparameter optimization feature that automatically searches the hyperparameter space (e.g., via Bayesian optimization) to find the configuration that maximizes a target metric, automating the search for optimal hyperparameters without manual trial and error.

**Exam Triggers:** "automatically find the best hyperparameters", "optimize a target metric like validation accuracy or loss", "run many training jobs with different learning rates/batch sizes".

**Not to be confused with:** SageMaker Clarify - AMT optimizes model **performance** via hyperparameter search, while Clarify evaluates **bias and explainability**; they address entirely different concerns and are never substitutes for each other.

**Security & FAQ Insights:** You define the hyperparameter ranges and an objective metric to optimize (e.g., validation accuracy or loss). Supports multiple search strategies: random, Bayesian optimization, and hyperband.

🔥⭐⭐⭐

---

## SageMaker Clarify

**Definition & Purpose:** A tool that detects statistical bias in datasets (pre-training) and model predictions (post-training), and explains individual predictions using SHAP values, supporting model fairness evaluation and explainability.

**Exam Triggers:** "check whether a model shows bias across demographic groups", "explain why a model produced a specific prediction", "SHAP values / feature attribution".

**Not to be confused with:** SageMaker Model Monitor - Clarify performs **point-in-time** bias/explainability analysis (typically before or right after training), while Model Monitor performs **continuous, live** monitoring of a production endpoint for drift over time. Decision criteria: **one-time bias/explainability check** -> Clarify; **ongoing production drift detection** -> Model Monitor.

**Security & FAQ Insights:** Pre-training bias metrics examine the dataset itself (e.g., class imbalance across groups); post-training metrics examine model predictions. Uses SHAP (SHapley Additive exPlanations) values to attribute a prediction to individual input features.

🔥⭐⭐⭐⭐

---

## SageMaker Model Cards

**Definition & Purpose:** A feature for documenting essential model information - intended use, training data, performance metrics, and risk considerations - in a centralized, structured format, supporting governance, audit, and responsible-AI documentation requirements.

**Exam Triggers:** "document a model's intended use and limitations for governance/audit", "responsible AI documentation before internal review".

**Not to be confused with:** SageMaker Model Registry - Model Cards document descriptive, governance-oriented information about a model (intended use, risks), while Model Registry tracks **versioning and the deployment approval workflow**. Decision criteria: need **descriptive governance documentation** -> Model Cards; need **version tracking and promotion approvals** -> Model Registry.

**Security & FAQ Insights:** Can auto-populate fields such as training job details and evaluation metrics directly from SageMaker. Supports both AWS-trained models and models trained outside SageMaker (imported documentation).

🔥⭐⭐

---

## SageMaker Model Registry

**Definition & Purpose:** A catalog for versioning trained models and managing their approval status through deployment stages (e.g., Pending -> Approved -> Deployed), tracking model versions and controlling promotion to production through an approval workflow.

**Exam Triggers:** "version models and control promotion to production", "approval workflow before a model is deployed", "track lineage across model iterations".

**Not to be confused with:** SageMaker Model Cards - Model Cards capture descriptive governance/responsible-AI documentation, while Model Registry specifically tracks version lineage and deployment **approval status**, which can trigger downstream automation.

**Security & FAQ Insights:** Groups related model versions into "model groups," making it easy to track lineage across iterations. Approval status can trigger downstream automation (e.g., CI/CD pipelines that deploy approved models).

🔥⭐⭐⭐

---

## SageMaker Real-Time Inference

**Definition & Purpose:** A persistent, always-on hosted endpoint that returns predictions synchronously with low latency, used to serve models that require immediate, interactive predictions. Standard integration: supports auto-scaling and multi-model/multi-container endpoints behind a single endpoint.

**Exam Triggers:** "interactive predictions with millisecond response times", "live API call must return a decision during checkout".

**Not to be confused with:** Amazon Bedrock - Bedrock provides serverless inference for foundation models via managed APIs with no infrastructure to manage, while Real-Time Inference hosts **your own model** on a dedicated endpoint that is billed continuously. Decision criteria: serving a **custom-trained model with low latency, continuously** -> Real-Time Inference; consuming a **pre-trained FM serverlessly** -> Bedrock.

**Security & FAQ Insights:** The endpoint runs continuously, so you pay for the underlying instances even when idle. Supports auto-scaling and multi-model/multi-container endpoints to serve several models behind one endpoint.

🔥⭐⭐⭐⭐

---

## SageMaker Asynchronous Inference

**Definition & Purpose:** A hosting option that queues incoming requests with large payloads and processes them in the background, scaling the endpoint down to zero when idle, used to handle large payloads or long-running inference without keeping an endpoint running continuously. Standard integration: **Amazon SNS** (completion notifications), **S3** (results storage).

**Exam Triggers:** "payloads up to 1 GB", "processing times up to one hour", "need an endpoint that scales to zero between requests but still queues work".

**Not to be confused with:** SageMaker Batch Transform - Batch Transform spins up temporary resources for **one bulk job over an entire dataset with no persistent endpoint**, while Asynchronous Inference maintains a **queued endpoint** that scales to zero between individual large-payload requests. Decision criteria: **single bulk scoring job, no endpoint needed** -> Batch Transform; **ongoing but infrequent large/long requests via an endpoint** -> Asynchronous Inference.

**Security & FAQ Insights:** Supports payloads up to 1 GB and processing times of up to one hour, far beyond real-time endpoint limits. Results are written to S3 and can trigger notifications via Amazon SNS when complete.

🔥⭐⭐⭐

---

## SageMaker Serverless Inference

**Definition & Purpose:** A hosting option that automatically provisions, scales, and turns off compute capacity based on traffic, scaling to zero when there are no requests, used to serve models with intermittent or unpredictable traffic without managing or paying for idle infrastructure.

**Exam Triggers:** "intermittent or unpredictable traffic", "avoid paying for idle infrastructure", "occasional use by an internal tool, cold starts acceptable".

**Not to be confused with:** SageMaker Asynchronous Inference - Asynchronous Inference targets **large payloads/long processing times** via a queued endpoint, while Serverless Inference targets **small, sporadic, near-real-time requests** with automatic scale-to-zero. Decision criteria: requests are **large/long-running** -> Asynchronous; requests are **small/sporadic and latency-tolerant** -> Serverless Inference.

**Security & FAQ Insights:** Cold starts add latency after idle periods, making it unsuitable for strict low-latency requirements. You pay only for the compute used to process requests, not for idle capacity.

🔥⭐⭐⭐

---

## SageMaker Batch Transform

**Definition & Purpose:** A hosting option that spins up temporary resources to run inference on an entire dataset stored in S3, then shuts the resources down once done, used to get predictions for a large dataset all at once without needing a persistent endpoint. Standard integration: **S3** (input dataset and output predictions).

**Exam Triggers:** "score an entire dataset at once", "nightly batch job that scores customer records and writes predictions back to S3", "no persistent endpoint needed".

**Not to be confused with:** SageMaker Asynchronous Inference - Asynchronous Inference maintains a **scalable endpoint** that processes individual queued requests, while Batch Transform creates **no endpoint at all** and exists only for the duration of one bulk job. Decision criteria: **one-off or periodic scoring of a full dataset** -> Batch Transform; **endpoint-based handling of individual large/long requests over time** -> Asynchronous Inference.

**Security & FAQ Insights:** No persistent endpoint is created or billed - compute exists only for the duration of the job. Well suited for scenarios where predictions are not needed in real time (e.g., periodic scoring).

🔥⭐⭐⭐

---

## SageMaker Model Monitor

**Definition & Purpose:** A continuous validation service designed to audit the performance of real-time endpoints **live in production**, monitoring incoming payloads and alerting engineers (via CloudWatch) to operational drift (Data Drift and Concept Drift). Standard integration: **CloudWatch** (alerts on detected drift).

**Exam Triggers:** "detect data drift or concept drift in a production model", "get a CloudWatch alert when production input data diverges from training data".

**Not to be confused with:** SageMaker Clarify - Clarify performs point-in-time bias/explainability analysis (often before deployment), while Model Monitor continuously audits a **live production endpoint** over time. Decision criteria: **ongoing production monitoring for drift** -> Model Monitor; **one-time bias/explainability assessment** -> Clarify.

**Security & FAQ Insights:** Operates exclusively on real-time production endpoints, not on training or batch jobs. Data Drift = the statistical distribution of input data diverges from the training data; Concept Drift = the relationship between inputs and outputs changes over time, degrading prediction accuracy.

🔥⭐⭐⭐⭐

---

# Reference FAQs

- [Amazon Bedrock FAQs](https://aws.amazon.com/bedrock/faqs/)
- [Amazon SageMaker FAQs](https://aws.amazon.com/sagemaker/faqs/)
- [Amazon SageMaker AI FAQs](https://aws.amazon.com/sagemaker/ai/faqs/)
- [Amazon Comprehend FAQs](https://aws.amazon.com/comprehend/faqs/)
- [Amazon Textract FAQs](https://aws.amazon.com/textract/faqs/)
- [Amazon Transcribe FAQs](https://aws.amazon.com/transcribe/faqs/)
- [Amazon Translate FAQs](https://aws.amazon.com/translate/faqs/)
- [Amazon Lex FAQs](https://aws.amazon.com/lex/faqs/)
- [Amazon Personalize FAQs](https://aws.amazon.com/personalize/faqs/)
- [Amazon Kendra FAQs](https://aws.amazon.com/kendra/faqs/)
- [Amazon Forecast](https://aws.amazon.com/forecast/)
- [AWS Data FAQs](https://aws.amazon.com/data/#faqs)
- [AWS](https://aws.amazon.com/)
