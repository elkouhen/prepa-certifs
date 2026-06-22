# AWS AI Practitioner - Ultimate Cheat Sheet

## How This Cheat Sheet Is Built (Methodology)

This revision guide is structured for the AWS AI/ML certification exam. Each entry documents exactly one AWS service, or one key Amazon Bedrock / Amazon SageMaker feature, and follows the same structure throughout the document.

---

## Scope and Ordering Rules

**Rule 1 - Scope (one service = one section)**  
Each section covers exactly one AWS service or one key Amazon Bedrock / Amazon SageMaker feature that is supported by an official AWS FAQ or AWS documentation reference listed in **Reference FAQs** at the end of this document. Multiple services must not be grouped into a single section.

**Rule 2 - Order (logical AI/ML pipeline)**  
Services are ordered to reflect a typical AI/ML pipeline:
- **Part 1 - Pre-trained AI services:** consume data with no ML expertise required.
- **Part 2 - Generative AI with Amazon Bedrock:** foundation models, RAG, agents, guardrails, customization, and evaluation.
- **Part 3 - Custom ML with Amazon SageMaker:** data preparation, training, tuning, deployment, governance, and monitoring.

---

## Mandatory Structure for Every Entry

Every entry must use exactly this structure and order:

### [Service name]

#### Definition & Purpose
2-3 sentences explaining what the service does, why it is used, which native AWS integrations it commonly supports, and whether the deployment model is serverless, fully managed, or dedicated infrastructure.

#### Exam Triggers
Bullet list of keywords, exact phrases, or constraints in an exam question that point directly to this service.

#### Not to be confused with (MANDATORY)
Identify the closest AWS service that could be confused with this one, then state the decision rule in this exact form:

▎ Choose [this service] if ... / Choose [other service] if ...

#### Security & FAQ Insights
- Critical security patterns: data isolation, KMS encryption, VPC endpoints, IAM roles, and compliance where relevant.
- FAQ-specific limitations: quotas, supported formats, regions, unsupported cases, or pricing constraints.
- Common exam traps linked to this service.

#### 🔥 Exam Weight
⭐⭐⭐⭐⭐ — Estimated exam importance from 1 to 5 stars, with a one-line justification.

---

# Part 1 - AWS Managed AI Services (Pre-built AI)

## Amazon Textract

**Scope:** `CONSUME`

### Definition & Purpose
An OCR and document-understanding service that extracts printed text, handwriting, tables, forms, key-value pairs, queries, invoices/receipts, and identity-document fields from scanned documents and images. The FAQ emphasizes that Textract goes beyond OCR by preserving document structure, returning confidence scores, supporting Queries/Custom Queries, and offering specialized APIs such as Detect Document Text, Analyze Document, Analyze Expense, and Analyze ID. Standard integrations: **S3** (input/output JSON), **SNS/SQS** (async job notifications), **Lambda** (orchestration), and **Amazon A2I** (human review); deployment model: fully managed serverless AI API.

**Official FAQ/reference:** https://aws.amazon.com/textract/faqs/

### Exam Triggers
- "extract text/tables/forms from scanned documents or images"
- "automate invoice/receipt processing"
- "extract data from IDs/passports/driver's licenses"
- "OCR at scale"

### Not to be confused with (MANDATORY)
Amazon Comprehend - Textract performs OCR/extraction (turning a document image into raw text and structured fields), while Comprehend analyzes text that already exists (sentiment, entities, PII) to extract meaning.

▎ Choose Amazon Textract if the input is a scanned document, image, PDF, form, table, invoice, receipt, or ID that needs OCR and structured extraction / Choose Amazon Comprehend if the input is already digital text and the task is sentiment analysis, entity detection, PII detection, or key-phrase extraction.

### Security & FAQ Insights
- Analyze Document (text/forms/tables), Analyze Expense (receipts/invoices: vendor, total, line items), and Analyze ID (driver's licenses/passports) are distinct specialized APIs.
- Output can be encrypted with KMS; supports VPC endpoints for private access; Amazon A2I provides a managed human-review loop for low-confidence results.

### 🔥 Exam Weight
⭐⭐⭐⭐ — High-value exam topic; commonly appears in scenario questions and service-comparison traps.

---

## Amazon Comprehend

**Scope:** `CONSUME`

### Definition & Purpose
An NLP service that uses ML to extract insights from text, including sentiment, entities, key phrases, language, syntax, topics, PII, custom classification, and custom entity recognition. The FAQ highlights voice-of-customer analytics, semantic search, and knowledge discovery as core use cases, where Comprehend indexes meaning and context rather than just keywords. Standard integrations: **S3** (batch jobs), **Lambda** (event-driven analysis), **Kinesis Data Firehose** (inline PII detection/redaction), and **Textract** (analyzing extracted document text); deployment model: fully managed serverless NLP API.

**Official FAQ/reference:** https://aws.amazon.com/comprehend/faqs/

### Exam Triggers
- "detect sentiment in customer feedback/emails"
- "identify and redact PII in text"
- "extract entities or key phrases from unstructured text"
- "classify documents into custom categories"

### Not to be confused with (MANDATORY)
Amazon Textract - Comprehend analyzes text that is already in digital form, while Textract is required first if the source is a scanned document or image. Also distinct from **Comprehend Medical**, a specialized variant for clinical text (conditions, medications, dosages).

▎ Choose Amazon Comprehend if the text already exists and must be analyzed for sentiment, entities, key phrases, language, syntax, PII, or classification / Choose Amazon Textract if the source is a scanned document, image, PDF, form, table, invoice, receipt, or ID that must first be converted into text/fields.

### Security & FAQ Insights
- Comprehend Custom allows training custom classifiers and entity recognizers on your own labeled data.
- PII detection/redaction can run directly inline within a Kinesis Data Firehose delivery stream.
- Supports VPC endpoints and KMS encryption for custom model artifacts and analysis jobs.

### 🔥 Exam Weight
⭐⭐⭐⭐ — High-value exam topic; commonly appears in scenario questions and service-comparison traps.

---

## Amazon Transcribe

**Scope:** `CONSUME`

### Definition & Purpose
An automatic speech recognition (ASR) service that converts audio and video into text for both real-time streaming and asynchronous batch transcription. The FAQ separates general transcription from Transcribe Medical and stresses that medical output is not a substitute for professional review and may not fully de-identify protected health information. Standard integrations: **S3** (batch audio input/output), **Kinesis Video/Audio Streams** (real-time transcription), **Amazon Connect** (call-center transcription), and **Amazon Comprehend** (text analytics on transcripts); deployment model: fully managed serverless speech-to-text API.

**Official FAQ/reference:** https://aws.amazon.com/transcribe/faqs/

### Exam Triggers
- "transcribe call-center recordings"
- "generate subtitles/captions"
- "redact PII (e.g., credit card numbers) from spoken audio"
- "convert speech to text in real time"

### Not to be confused with (MANDATORY)
Amazon Translate - Translate converts text between written languages, it does not process audio. Also distinct from **Amazon Polly**, which performs the reverse operation (text-to-speech).

▎ Choose Amazon Transcribe if the source is audio or video and must be converted into text, either in real time or batch / Choose Amazon Translate if the source is already text and must be converted into another written language.

### Security & FAQ Insights
- Custom vocabularies and language models improve accuracy on domain-specific terms (product names, jargon).
- Automatic PII identification/redaction is built in.
- **Transcribe Medical** is a specialized variant for clinical documentation.
- Supports VPC endpoints for private API access.

### 🔥 Exam Weight
⭐⭐⭐ — Moderate exam topic; useful for recognizing targeted service triggers and common differentiators.

---

## Amazon Translate

**Scope:** `CONSUME`

### Definition & Purpose
A neural machine translation service that translates text and documents between languages in real time or in batch for localization and multilingual applications. The FAQ notes support for 75 languages and positions Active Custom Translation (ACT) as the built-in way to improve domain-specific terminology with parallel data without managing a custom translation model. Standard integrations: **S3** (batch document translation), **Amazon Comprehend** (language detection), and application-layer services such as **CloudFront** and **Lambda** for dynamic localization; deployment model: fully managed serverless neural translation API.

**Official FAQ/reference:** https://aws.amazon.com/translate/faqs/

### Exam Triggers
- "translate text or documents between languages"
- "build a multilingual application"
- "improve translation quality for domain-specific terminology without training a custom model"

### Not to be confused with (MANDATORY)
Amazon Transcribe - Translate works on written text, never on audio. Decision criteria for customization: need higher-quality translations for a specific domain using your own parallel data **without** a full custom model -> **Active Custom Translation (ACT)**; need full model training -> not supported by Translate (would require SageMaker).

▎ Choose Amazon Translate if the input is written text or documents that must be translated between languages / Choose Amazon Transcribe if the input is audio or video that must first be converted into text.

### Security & FAQ Insights
- Active Custom Translation (ACT) improves domain-specific translation quality using your own parallel data, without training or managing a full custom model.
- Batch translation jobs and stored data can be encrypted via KMS.

### 🔥 Exam Weight
⭐⭐⭐ — Moderate exam topic; useful for recognizing targeted service triggers and common differentiators.

---

## Amazon Lex

**Scope:** `BUILD` · `DEPLOY`

### Definition & Purpose
A service for building conversational interfaces, chatbots, and voice bots using intent recognition, utterances, slots, prompts, and fulfillment logic. The FAQ frames Lex as a managed text and speech request service for structured bot interactions, including contact-center and self-service automation use cases. Standard integrations: **Amazon Connect** (contact-center bots), **AWS Lambda** (intent fulfillment), **Amazon Cognito** (user authentication), and **CloudWatch/S3** (conversation logs); deployment model: fully managed conversational AI service.

**Official FAQ/reference:** https://aws.amazon.com/lex/faqs/

### Exam Triggers
- "build a chatbot or voice bot"
- "manage dialogue with intents, utterances, and slots"
- "deflect contact-center calls with a virtual agent"

### Not to be confused with (MANDATORY)
Amazon Q Business / Bedrock Agents - Lex manages structured, intent/slot-based conversational flows for well-defined tasks (check balance, book appointment), whereas Q Business and Bedrock Agents provide open-ended generative reasoning over broad knowledge sources.

▎ Choose Amazon Lex if the bot follows a fixed intent/slot dialogue for known tasks such as booking, routing, or account lookup / Choose Amazon Q Business / Bedrock Agents if the application needs open-ended generative Q&A, broad knowledge synthesis, or autonomous multi-step reasoning.

### Security & FAQ Insights
- Bots are defined using intents, sample utterances, and slots (the information needed to fulfill an intent).
- Conversation logs can be encrypted with KMS and sent to CloudWatch/S3.
- Integrates with Amazon Cognito for end-user authentication within bots.

### 🔥 Exam Weight
⭐⭐⭐ — Moderate exam topic; useful for recognizing targeted service triggers and common differentiators.

---

## Amazon Kendra

**Scope:** `CONSUME`

### Definition & Purpose
An intelligent enterprise search service powered by ML that lets users ask natural-language questions across corporate content repositories and receive precise answers, passages, or ranked documents. The FAQ emphasizes search over manuals, research reports, FAQs, HR documentation, customer-service guides, and systems such as **S3**, SharePoint, Salesforce, ServiceNow, RDS databases, and OneDrive. Standard integrations include 30+ built-in connectors and optional use as a retrieval backend for **Amazon Q Business**; deployment model: fully managed enterprise search service.

**Official FAQ/reference:** https://aws.amazon.com/kendra/faqs/

### Exam Triggers
- "natural-language search/Q&A across internal documents"
- "enterprise search spanning multiple data silos"
- "employees ask questions and get precise answers from internal documents"

### Not to be confused with (MANDATORY)
Amazon Q Business - Q Business is a full generative AI assistant that synthesizes conversational answers (and can use Kendra as a retriever underneath), whereas Kendra itself is a search/retrieval engine that returns relevant documents/passages.

▎ Choose Amazon Kendra if the requirement is semantic enterprise search that returns ranked documents, passages, or precise answers from indexed repositories / Choose Amazon Q Business if the requirement is a full generative assistant that synthesizes conversational answers and can take broader actions.

### Security & FAQ Insights
- Per-document Access Control Lists (ACLs) reflect the permissions of the source repository, ensuring users only see results they're authorized to access.
- Supports KMS encryption and VPC deployment for private indexing/search.

### 🔥 Exam Weight
⭐⭐⭐ — Moderate exam topic; useful for recognizing targeted service triggers and common differentiators.

---

## Amazon Personalize

**Scope:** `BUILD` · `DEPLOY`

### Definition & Purpose
A fully managed ML service that uses user, item, and interaction data to train private personalization models for product/content recommendations, personalized ranking, and next-best-action experiences. The FAQ stresses that it uses the same ML technology as Amazon.com, supports real-time personalization from live behavior, and can start with use-case optimized recommenders or configurable custom resources. Standard integrations: **S3** (historical datasets), **Kinesis** (real-time interaction events), and **Amazon Pinpoint** (personalized campaigns); deployment model: fully managed personalization service.

**Official FAQ/reference:** https://aws.amazon.com/personalize/faqs/

### Exam Triggers
- "show personalized product/content recommendations"
- "next-best-action recommendations"
- "real-time personalization based on user behavior"

### Not to be confused with (MANDATORY)
Amazon Forecast - Forecast predicts future numeric values (demand, inventory) over time, while Personalize generates individualized recommendations per user.

▎ Choose Amazon Personalize if the output is an individualized recommendation, ranking, or next-best action for a specific user or item / Choose Amazon Forecast / SageMaker Canvas if the output is a future numeric value such as demand, inventory, or resource needs over time.

### Security & FAQ Insights
- Provides pre-built "recipes" for common use cases (product recommendations, related items, personalized re-ranking, next-best-action).
- Recommendations can be returned in real time via API or generated in batch.
- Data is encrypted at rest via KMS, and training requires a minimum volume of historical interaction data.

### 🔥 Exam Weight
⭐⭐⭐ — Moderate exam topic; useful for recognizing targeted service triggers and common differentiators.

---

## Amazon Forecast

**Scope:** `BUILD` · `DEPLOY`

### Definition & Purpose
A managed time-series forecasting service that used ML to predict future numeric values such as demand, inventory, staffing, or capacity from historical time-series data. The current FAQ/exam-relevant caveat is lifecycle positioning: Forecast remains relevant for existing customers, while AWS directs new no-code forecasting use cases toward SageMaker Canvas. Standard integration: **S3** for historical target time series, related time series, and item metadata inputs; deployment model: fully managed forecasting service for existing customers.

**Official FAQ/reference:** https://aws.amazon.com/forecast/

### Exam Triggers
- "predict future demand/inventory from historical time-series data"

### Not to be confused with (MANDATORY)
SageMaker Canvas - Amazon Forecast is **no longer onboarding new customers**; AWS positions SageMaker Canvas as its no-code successor for time-series forecasting use cases.

▎ Choose Amazon Forecast if the scenario is for an existing Forecast customer maintaining existing workloads / Choose SageMaker Canvas if the scenario is a new no-code time-series forecasting project, because AWS positions Canvas as the forward-looking option.

### Security & FAQ Insights
- Exam trap - existing customers can continue using Forecast, but its forecasting capabilities are being consolidated into Amazon SageMaker Canvas, which is the forward-looking answer on the exam.

### 🔥 Exam Weight
⭐⭐ — Lower-frequency topic; mainly tested through niche limitations or modern AWS positioning.

---

# Part 2 - Amazon Bedrock (Generative AI)

## Amazon Bedrock

**Scope:** `CONSUME`

### Definition & Purpose
A fully managed, serverless service providing unified API access to foundation models from Amazon and third-party providers without provisioning model-hosting infrastructure. The FAQ emphasizes model choice, serverless inference, data isolation, model customization, agents, knowledge bases, guardrails, model evaluation, and Bedrock Data Automation for multimodal extraction workflows. Standard integrations: **IAM**, **KMS**, **AWS PrivateLink**, **CloudTrail**, **Bedrock Knowledge Bases**, **Bedrock Agents**, and **Guardrails**; deployment model: fully managed serverless foundation-model service.

**Official FAQ/reference:** https://aws.amazon.com/bedrock/faqs/

### Exam Triggers
- "access multiple foundation models through a single API without managing infrastructure"
- "compare/test models from different providers"
- "serverless generative AI"

### Not to be confused with (MANDATORY)
Amazon SageMaker - SageMaker is for building, training, and hosting **custom** models where the customer manages the underlying infrastructure (instances, clusters), while Bedrock provides serverless API access to **pre-trained** FMs.

▎ Choose Amazon Bedrock if you need serverless API access to pre-trained foundation models without managing infrastructure / Choose Amazon SageMaker if you need to build, train, fine-tune, host, or control infrastructure for custom models.

### Security & FAQ Insights
- Five pricing models - **On-Demand** (pay per token, no commitment), **Flex Tier** (~50% discount for non-urgent workloads), **Priority Tier** (~75% premium for fastest response), **Batch Inference** (~50% cheaper for asynchronous bulk jobs), and **Provisioned Throughput** (capacity units billed hourly, required for some customized model families).
- AWS does not use customer prompts/outputs to train the underlying base models (data isolation).
- PrivateLink enables VPC-only access without traversing the public internet; CloudTrail logs every API call for auditability; KMS encrypts custom model artifacts.

### 🔥 Exam Weight
⭐⭐⭐⭐⭐ — Core certification topic; frequently tested for service selection, security boundaries, and architectural tradeoffs.

---

## Bedrock Knowledge Bases

**Scope:** `BUILD` · `DEPLOY`

### Definition & Purpose
A fully managed RAG (Retrieval-Augmented Generation) feature that ingests enterprise content, chunks documents, generates embeddings, stores vectors, and retrieves relevant context to ground foundation-model responses. The FAQ connects Knowledge Bases to Bedrock Data Automation standard output for documents and multimodal extraction, making it the managed path when source content must be prepared and grounded without building a custom embeddings pipeline. Standard integrations: **S3** sources, vector stores such as **Amazon OpenSearch Serverless**, **Aurora/RDS PostgreSQL pgvector**, **Neptune Analytics** for GraphRAG, and **Bedrock Agents**; deployment model: fully managed Bedrock feature backed by customer-selected vector stores.

**Official FAQ/reference:** https://aws.amazon.com/bedrock/faqs/

### Exam Triggers
- "ground LLM answers in company documents"
- "implement RAG without managing an embeddings pipeline"
- "internal assistant answering questions from S3-stored policies"

### Not to be confused with (MANDATORY)
Amazon Kendra - Kendra is a general-purpose search/retrieval engine returning ranked documents to a user or application, while Knowledge Bases is purpose-built to retrieve chunks and inject them directly into an LLM prompt for generation. Also distinct from **Bedrock Agents**, which can use a Knowledge Base as one of several tools in a broader multi-step workflow.

▎ Choose Bedrock Knowledge Bases if retrieved chunks must be injected into an LLM prompt to ground a generated answer with RAG / Choose Amazon Kendra if users need an enterprise search index that returns ranked documents or passages directly.

### Security & FAQ Insights
- Workflow: ingest from S3 -> chunk documents -> generate embeddings -> store in chosen vector database -> retrieve relevant chunks at query time.
- Data remains within the customer's selected vector store and can be encrypted via KMS; ingestion jobs sync incrementally from the S3 source.

### 🔥 Exam Weight
⭐⭐⭐⭐⭐ — Core certification topic; frequently tested for service selection, security boundaries, and architectural tradeoffs.

---

## Bedrock Agents

**Scope:** `BUILD` · `DEPLOY`

### Definition & Purpose
Orchestration components that use a foundation model to interpret user requests, plan multi-step tasks, call tools, and return a final answer or action. The FAQ positions Agents as the Bedrock feature for executing business workflows by combining instructions, action groups, APIs, Lambda functions, optional knowledge bases, and session context. Standard integrations: **AWS Lambda** action groups, **Bedrock Knowledge Bases**, OpenAPI schemas, **IAM** execution roles, and **Bedrock Guardrails**; deployment model: fully managed Bedrock orchestration feature.

**Official FAQ/reference:** https://aws.amazon.com/bedrock/faqs/

### Exam Triggers
- "automate a complex multi-step business workflow"
- "agent that plans tasks, calls APIs, and acts autonomously"
- "orchestrate reasoning across multiple tools"

### Not to be confused with (MANDATORY)
Bedrock Knowledge Bases - Knowledge Bases provide passive retrieval to ground a single response, while Agents actively plan a sequence of steps, execute them (including calling APIs), observe results, and loop until the task is complete.

▎ Choose Bedrock Agents if the application must plan and execute multi-step workflows, call APIs through Lambda, and use tools autonomously / Choose Bedrock Knowledge Bases if the application only needs retrieval to ground a single generated response.

### Security & FAQ Insights
- Core components: Instructions (persona/goals), Action Groups (APIs/Lambda functions defined via OpenAPI schemas, executed with scoped IAM permissions), and optional Knowledge Bases.
- Bedrock Guardrails can be applied to both agent inputs and outputs; agents support session-based memory across a conversation.

### 🔥 Exam Weight
⭐⭐⭐⭐⭐ — Core certification topic; frequently tested for service selection, security boundaries, and architectural tradeoffs.

---

## Bedrock Guardrails

**Scope:** `GOVERN` · `DEPLOY`

### Definition & Purpose
An independent policy layer that evaluates prompts and model responses to enforce responsible-AI controls before, during, or after Bedrock model interactions. The FAQ describes configurable guardrail policies for denied topics, content filters, word filters, sensitive-information handling, contextual grounding, and relevance checks, reusable across supported models and agents. Standard integrations: **Bedrock model invocations**, **Bedrock Agents**, **Knowledge Bases/RAG grounding**, **IAM**, and audit/logging controls; deployment model: fully managed Bedrock safety and policy layer.

**Official FAQ/reference:** https://aws.amazon.com/bedrock/faqs/

### Exam Triggers
- "prevent a chatbot from discussing certain topics"
- "redact PII from model responses"
- "enforce Responsible AI / content-safety policy on a generative AI application"

### Not to be confused with (MANDATORY)
Amazon Comprehend (PII detection) - Comprehend is a general-purpose text-analysis service usable anywhere, while Guardrails is purpose-built and natively wired into the Bedrock model-invocation pipeline (including denied topics, content filters, and RAG grounding checks).

▎ Choose Bedrock Guardrails if policy enforcement, denied topics, content filters, PII masking, or RAG grounding checks must be applied directly inside Bedrock model/agent calls / Choose Amazon Comprehend if you need standalone NLP analysis such as PII detection or sentiment analysis on arbitrary text outside the Bedrock invocation path.

### Security & FAQ Insights
- Configurable policy types: denied topics, content filters (hate, violence, etc.), PII redaction/masking, custom word filters, and grounding/relevance checks for RAG outputs.
- Applied independently to both the prompt (input) and the model's response (output), and can be reused across multiple models and agents.

### 🔥 Exam Weight
⭐⭐⭐⭐⭐ — Core certification topic; frequently tested for service selection, security boundaries, and architectural tradeoffs.

---

## Bedrock Fine-tuning

**Scope:** `TUNE`

### Definition & Purpose
A customization method that adapts a supported foundation model using labeled prompt-response examples to improve task-specific accuracy, tone, style, or output format. The FAQ distinguishes fine-tuning from prompt engineering and continued pre-training: fine-tuning uses supervised examples and the resulting custom model is private to the customer. Standard integrations: **S3** (training/validation data), **KMS** (artifact encryption), IAM permissions, and **Provisioned Throughput** for serving customized models; deployment model: fully managed Bedrock model-customization workflow.

**Official FAQ/reference:** https://aws.amazon.com/bedrock/faqs/

### Exam Triggers
- "improve a model's accuracy/consistency on a specific task using your own labeled examples"
- "force a model to always respond in a specific format"

### Not to be confused with (MANDATORY)
Bedrock Continued Pre-training - Continued Pre-training uses large volumes of **unlabeled** domain text to inject broad domain knowledge, while Fine-tuning uses **labeled** prompt-response pairs to shape behavior for a specific task.

▎ Choose Bedrock Fine-tuning if you have labeled prompt-response examples and want task-specific behavior or output format consistency / Choose Bedrock Continued Pre-training if you have a large unlabeled domain corpus and want to inject broad vocabulary or domain knowledge.

### Security & FAQ Insights
- Exam trap - fine-tuned models generally require Provisioned Throughput, but **Amazon Nova fine-tuned models support On-Demand inference** (pay per token, no commitment required). Older model families (Titan, etc.) still require Provisioned Throughput.
- Training data is stored encrypted in S3 via KMS, and the resulting fine-tuned model weights remain private to the customer's account.

### 🔥 Exam Weight
⭐⭐⭐⭐ — High-value exam topic; commonly appears in scenario questions and service-comparison traps.

---

## Bedrock Continued Pre-training

**Scope:** `TUNE`

### Definition & Purpose
A customization method that continues training a supported foundation model on large volumes of unlabeled domain-specific text to inject vocabulary, style, and broad domain knowledge. The FAQ positions continued pre-training as a different customization path from fine-tuning because it changes domain familiarity before any supervised task-specific tuning. Standard integrations: **S3** for the unlabeled corpus, **KMS** for artifacts, IAM controls, and **Provisioned Throughput** for serving the resulting custom model; deployment model: fully managed Bedrock model-customization workflow.

**Official FAQ/reference:** https://aws.amazon.com/bedrock/faqs/

### Exam Triggers
- "inject domain-specific vocabulary/knowledge into a model before fine-tuning"
- "continue pre-training on internal technical documentation"

### Not to be confused with (MANDATORY)
Bedrock Fine-tuning - Fine-tuning requires labeled prompt-response pairs for a specific task, while Continued Pre-training uses large amounts of unlabeled data for broad knowledge injection and is often performed **before** fine-tuning.

▎ Choose Bedrock Continued Pre-training if you have large volumes of unlabeled domain text and need broad domain adaptation before task-specific tuning / Choose Bedrock Fine-tuning if you have labeled input/output examples for a specific task or response style.

### Security & FAQ Insights
- Exam trap - continued pre-trained models generally require Provisioned Throughput, but **Amazon Nova continued pre-trained models support On-Demand inference**. Older model families (Titan, etc.) still require Provisioned Throughput.
- The resulting model remains private to the customer's account.

### 🔥 Exam Weight
⭐⭐⭐ — Moderate exam topic; useful for recognizing targeted service triggers and common differentiators.

---

## Bedrock Custom Model Import

**Scope:** `DEPLOY`

### Definition & Purpose
A feature that imports supported externally trained model weights into Bedrock so they can be invoked through Bedrock APIs and governed with Bedrock security controls. The FAQ/exam point is that import is not arbitrary hosting: only supported architectures and packaging formats qualify, and the imported model runs as a private Bedrock custom model rather than a SageMaker endpoint. Standard integrations: Bedrock unified invocation APIs, IAM, KMS, CloudTrail, and Bedrock operational controls; deployment model: Bedrock-managed serverless serving for supported imported model architectures.

**Official FAQ/reference:** https://aws.amazon.com/bedrock/faqs/

### Exam Triggers
- "run your own or a third-party open-source model within Bedrock's serverless environment"
- "import a custom fine-tuned Llama/Mistral model"

### Not to be confused with (MANDATORY)
SageMaker JumpStart - JumpStart deploys open-source models to a **self-managed SageMaker endpoint** (customer controls infrastructure), whereas Custom Model Import serves them through **Bedrock's serverless** environment.

▎ Choose Bedrock Custom Model Import if you want an externally trained supported model served through Bedrock serverless APIs and Bedrock security controls / Choose SageMaker JumpStart if you want to deploy an open-source model to customer-managed SageMaker endpoints with full infrastructure control.

### Security & FAQ Insights
- Only supports specific open-source architectures — not arbitrary custom architectures. Supported families include Llama 2/3/3.1/3.2/3.3, Mistral, Mixtral, Flan (T5-based), Qwen2/2.5/3, GPTBigCode, IBM Granite, and others; the list expands over time.
- Imported models are billed and scaled exactly like other Bedrock models.

### 🔥 Exam Weight
⭐⭐⭐ — Moderate exam topic; useful for recognizing targeted service triggers and common differentiators.

---

## Bedrock Model Distillation

**Scope:** `TUNE`

### Definition & Purpose
A customization method that creates a smaller, lower-cost "student" model by training it to imitate the outputs of a larger "teacher" model. The FAQ/exam value is cost-performance optimization for high-volume generative AI workloads where some quality tradeoff is acceptable in exchange for lower latency and inference cost. Standard workflow uses teacher-generated responses as training data and produces a private optimized model; deployment model: fully managed Bedrock customization workflow.

**Official FAQ/reference:** https://aws.amazon.com/bedrock/faqs/

### Exam Triggers
- "reduce inference latency/cost for a high-volume application while retaining quality"
- "distill a large model into a smaller one"

### Not to be confused with (MANDATORY)
Bedrock Fine-tuning - Fine-tuning adapts a model to a task using **your own labeled data**, whereas Distillation transfers the **behavior of an existing larger model** into a smaller model using the teacher's generated responses as training data.

▎ Choose Bedrock Model Distillation if you want a smaller, cheaper, lower-latency student model that imitates a larger teacher model / Choose Bedrock Fine-tuning if you want to adapt a model to your own labeled examples for a task-specific behavior.

### Security & FAQ Insights
- The teacher model's responses to a set of prompts become the training data for the student model.
- Trades some accuracy for significantly lower latency and cost compared to the teacher.

### 🔥 Exam Weight
⭐⭐⭐ — Moderate exam topic; useful for recognizing targeted service triggers and common differentiators.

---

## Bedrock Model Evaluation

**Scope:** `EVALUATE`

### Definition & Purpose
A managed capability to evaluate, score, and compare foundation models, customized models, or model-powered applications against quality, safety, robustness, and business-fit criteria. The FAQ separates automatic evaluation with curated or customer datasets from human evaluation workflows where internal teams or AWS-managed workers judge subjective criteria. Standard integrations: Bedrock-accessible models, built-in/custom datasets, metrics such as ROUGE/BLEU/BERTScore/toxicity, and human review workflows; deployment model: fully managed Bedrock evaluation workflow.

**Official FAQ/reference:** https://aws.amazon.com/bedrock/faqs/

### Exam Triggers
- "objectively compare candidate foundation models"
- "measure whether an FM application meets the business requirement"
- "ROUGE/BLEU/BERTScore/LLM-as-a-judge"

### Not to be confused with (MANDATORY)
SageMaker Clarify - Clarify evaluates **bias and explainability** for custom ML models trained in SageMaker, while Bedrock Model Evaluation benchmarks the **quality/output** of foundation models and GenAI applications.

▎ Choose Bedrock Model Evaluation if you need to compare foundation model outputs, quality, safety, or business fit using automatic or human evaluation / Choose SageMaker Clarify if you need bias detection, fairness analysis, or explainability for a custom ML model trained or evaluated in SageMaker.

### Security & FAQ Insights
- Automatic evaluation computes metrics such as accuracy, robustness, and toxicity using built-in or custom datasets.
- Human evaluation uses internal teams or AWS-managed workers to score subjective qualities (tone, brand alignment, creativity).
- Common metrics: ROUGE and BLEU (summarization/translation overlap), BERTScore (semantic similarity), and LLM-as-a-judge.

### 🔥 Exam Weight
⭐⭐⭐⭐ — High-value exam topic; commonly appears in scenario questions and service-comparison traps.

---

## Amazon Nova Family

**Scope:** `CONSUME`

### Definition & Purpose
Amazon's proprietary multimodal foundation-model family available through Bedrock, spanning low-cost/low-latency text models, more capable reasoning models, and media-generation models. The FAQ/exam relevance is cost-performance selection inside Bedrock: Nova Micro/Lite/Pro/Premier cover text tiers, while Nova Canvas and Nova Reel cover image and video generation. Standard integration: Bedrock unified APIs with the same **IAM**, **KMS**, **PrivateLink**, CloudTrail, guardrail, and evaluation controls; deployment model: fully managed Amazon foundation models accessed through Bedrock serverless APIs.

**Official FAQ/reference:** https://aws.amazon.com/bedrock/faqs/

### Exam Triggers
- "lowest-cost Amazon model for a high-volume, low-cost use case"
- "most capable Amazon model for complex tasks"
- "Amazon model for image generation/editing"
- "Amazon model for video generation"

### Not to be confused with (MANDATORY)
Amazon Titan - Titan is Amazon's older proprietary Bedrock model family, with Nova positioned as its successor across text and media generation tiers.

▎ Choose Amazon Nova Family if the exam asks for Amazon Bedrock models across modern text, image, or video tiers such as Nova Micro, Lite, Pro, Premier, Canvas, or Reel / Choose Amazon Titan if the question specifically names Titan embeddings or older Titan text/image model choices.

### Security & FAQ Insights
- Text models scale from Nova Micro (fastest/cheapest) to Nova Lite, Nova Pro, and Nova Premier (most capable).
- Creative/media models: Nova Canvas (image generation/editing) and Nova Reel (video generation).
- Inherits all standard Bedrock security controls (IAM, KMS, PrivateLink, CloudTrail).

### 🔥 Exam Weight
⭐⭐ — Lower-frequency topic; mainly tested through niche limitations or modern AWS positioning.

---

# Part 3 - Amazon SageMaker (Custom ML)

## Amazon SageMaker

**Scope:** `BUILD` · `TRAIN` · `TUNE` · `EVALUATE` · `DEPLOY` · `MONITOR` · `GOVERN`

### Definition & Purpose
A fully managed ML platform for building, training, tuning, deploying, governing, and monitoring custom ML models and foundation-model workloads. The FAQ now frames SageMaker as a unified AI and analytics experience spanning SageMaker Unified Studio, SageMaker AI, Data and AI Governance, and a lakehouse architecture across S3 data lakes, Redshift warehouses, Glue/Lake Formation catalogs, and federated sources. Standard integrations: **S3**, **Glue/Athena/Redshift/EMR**, **ECR**, **VPC**, **IAM**, **KMS**, and **CloudWatch**; deployment model: fully managed ML platform with customer-selected managed or dedicated compute.

**Official FAQ/reference:** https://aws.amazon.com/sagemaker/faqs/

### Exam Triggers
- "train a custom model on proprietary/transaction data"
- "need full control over training/hosting infrastructure"
- "build, train, tune, deploy, and monitor a custom fraud-detection model"

### Not to be confused with (MANDATORY)
Amazon Bedrock - Bedrock provides serverless API consumption of **pre-trained** FMs with no infrastructure to manage, while SageMaker requires choosing and configuring the underlying compute (instance types, clusters) to build **custom** models.

▎ Choose Amazon SageMaker if you need the full custom ML lifecycle with control over data prep, training, tuning, deployment, and monitoring infrastructure / Choose Amazon Bedrock if you only need serverless consumption of pre-trained foundation models through an API.

### Security & FAQ Insights
- Covers the full ML lifecycle: data preparation, build, train, tune, deploy, and monitor.
- Supports VPC-only training/hosting (no internet access), KMS encryption of data and model artifacts, and per-job/per-notebook IAM execution roles.

### 🔥 Exam Weight
⭐⭐⭐⭐⭐ — Core certification topic; frequently tested for service selection, security boundaries, and architectural tradeoffs.

---

## SageMaker Unified Studio

**Scope:** `BUILD`

### Definition & Purpose
A single web-based workspace that unifies data processing, SQL analytics, ML, and generative AI application development across AWS analytics and AI services. The FAQ specifically lists access from Unified Studio to Athena, AWS Glue, Amazon EMR, Amazon MWAA, Redshift, SageMaker AI, and Amazon Bedrock, with governance through SageMaker Catalog built on Amazon DataZone. Standard integrations: **Amazon DataZone**, **Glue**, **Athena**, **Redshift**, **EMR**, **MWAA**, **SageMaker AI**, and **Bedrock**; deployment model: fully managed web-based workspace.

**Official FAQ/reference:** https://aws.amazon.com/sagemaker/faqs/

### Exam Triggers
- "single entry point for data and ML teams to discover data, build pipelines, and train models"
- "unified governance across multiple analytics and ML consoles"

### Not to be confused with (MANDATORY)
SageMaker Studio - Studio is the ML-focused IDE (notebooks, code editor, pipelines), which is now one of the tools accessible **from within** Unified Studio's broader, project-based workspace.

▎ Choose SageMaker Unified Studio if the scenario spans data engineering, analytics, generative AI, ML collaboration, governance, and Amazon DataZone projects / Choose SageMaker Studio if the scenario is limited to an ML-specific IDE for notebooks, experiments, pipelines, and model development.

### Security & FAQ Insights
- Governance, access control, and cataloging across projects are managed through Amazon DataZone, providing a project-based permission model spanning previously siloed consoles.

### 🔥 Exam Weight
⭐⭐ — Lower-frequency topic; mainly tested through niche limitations or modern AWS positioning.

---

## SageMaker Studio

**Scope:** `BUILD`

### Definition & Purpose
A web-based ML IDE for data scientists to build, debug, train, tune, deploy, and monitor models using notebooks, code editing, experiments, and pipelines. In the FAQ's updated SageMaker positioning, Studio remains the ML-focused development environment available standalone and as part of the broader Unified Studio experience. Standard integrations: **SageMaker Pipelines**, experiments, debugger/profiler capabilities, model registry workflows, **S3**, **IAM**, and selectable compute instances; deployment model: fully managed web-based ML IDE with customer-selected compute.

**Official FAQ/reference:** https://aws.amazon.com/sagemaker/ai/faqs/

### Exam Triggers
- "single IDE for data scientists to write code, run experiments, and track results"
- "decouple notebook compute from storage for fast instance switching"

### Not to be confused with (MANDATORY)
SageMaker Unified Studio - Unified Studio is the broader, project-based workspace that wraps Studio together with data engineering and analytics tools; Studio itself remains the ML-specific IDE.

▎ Choose SageMaker Studio if the focus is an ML IDE for notebooks, code, experiments, pipelines, and model development / Choose SageMaker Unified Studio if the focus is a broader governed workspace across data engineering, analytics, ML, and Amazon DataZone projects.

### Security & FAQ Insights
- Studio Notebooks decouple compute from storage, allowing fast instance switching without losing notebook data.
- Integrates with SageMaker Pipelines for CI/CD-style ML workflows.

### 🔥 Exam Weight
⭐⭐⭐ — Moderate exam topic; useful for recognizing targeted service triggers and common differentiators.

---

## SageMaker Canvas

**Scope:** `BUILD` · `DEPLOY`

### Definition & Purpose
A no-code visual interface that lets business analysts build, evaluate, and use ML models for classification, regression, and time-series forecasting from tabular data without writing code. The FAQ/exam positioning makes Canvas the forward-looking no-code forecasting path for new use cases that previously pointed to Amazon Forecast. Standard integrations: data sources such as **S3** and tabular connectors plus **SageMaker Model Registry** for collaboration with data science teams; deployment model: fully managed no-code ML interface.

**Official FAQ/reference:** https://aws.amazon.com/sagemaker/ai/faqs/

### Exam Triggers
- "business analyst builds an ML model without writing code"
- "no-code time-series forecasting from a spreadsheet of historical data"

### Not to be confused with (MANDATORY)
Amazon Forecast - Forecast is no longer onboarding new customers, and AWS positions SageMaker Canvas as its **no-code successor** for time-series forecasting use cases.

▎ Choose SageMaker Canvas if a business analyst needs no-code classification, regression, or time-series forecasting for a new project / Choose Amazon Forecast if the scenario explicitly concerns an existing Amazon Forecast workload.

### Security & FAQ Insights
- Automatically tests multiple algorithms and selects the best-performing one for the dataset.
- Models built in Canvas can be registered in SageMaker Model Registry for collaboration with data science teams.

### 🔥 Exam Weight
⭐⭐⭐ — Moderate exam topic; useful for recognizing targeted service triggers and common differentiators.

---

## SageMaker JumpStart

**Scope:** `BUILD` · `DEPLOY`

### Definition & Purpose
A model hub that provides pre-trained models, foundation models, notebooks, and solution templates that can be fine-tuned and deployed through SageMaker. The FAQ/exam distinction is that JumpStart accelerates development while still deploying to SageMaker-managed/customer-controlled endpoints rather than Bedrock's serverless FM invocation layer. Standard integrations: **SageMaker endpoints**, **S3** artifacts, **IAM**, **VPC**, and training/inference jobs; deployment model: managed SageMaker deployment to customer-controlled endpoints.

**Official FAQ/reference:** https://aws.amazon.com/sagemaker/ai/faqs/

### Exam Triggers
- "deploy a pre-trained open-source model to your own endpoint and fine-tune it"
- "one-click access to open-source NLP/vision/tabular model templates"

### Not to be confused with (MANDATORY)
Bedrock Custom Model Import - Custom Model Import serves an open-source model through Bedrock's **serverless** environment, while JumpStart deploys it to a **self-managed SageMaker endpoint** where the customer controls infrastructure.

▎ Choose SageMaker JumpStart if you want one-click access to deploy, fine-tune, and host pre-trained open-source models on customer-managed SageMaker endpoints / Choose Bedrock Custom Model Import if you want the model served inside Bedrock as a serverless imported model.

### Security & FAQ Insights
- Models are deployed within the customer's VPC/endpoint with full infrastructure control (instance types, scaling).
- Covers NLP, vision, and tabular model templates, not just generative AI.

### 🔥 Exam Weight
⭐⭐⭐ — Moderate exam topic; useful for recognizing targeted service triggers and common differentiators.

---

## SageMaker Ground Truth

**Scope:** `BUILD`

### Definition & Purpose
A data labeling service that creates high-quality training labels using private workforces, third-party vendors, or Amazon Mechanical Turk, with optional automated labeling. The FAQ/exam value is active learning: SageMaker can automatically label easy examples and send uncertain examples to humans, reducing labeling cost and time. Standard integrations: **S3** input/output manifests, labeling workforces, SageMaker training datasets, and human review workflows; deployment model: fully managed data-labeling workflow with human workforce options.

**Official FAQ/reference:** https://aws.amazon.com/sagemaker/ai/faqs/

### Exam Triggers
- "label thousands of images/records for training"
- "combine automated labeling with human review to reduce cost"

### Not to be confused with (MANDATORY)
SageMaker Clarify - Ground Truth **creates** labeled training data, whereas Clarify **analyzes** existing labeled data and model predictions for bias and explainability.

▎ Choose SageMaker Ground Truth if you need to create high-quality labels for training data using humans, vendors, Mechanical Turk, or active learning / Choose SageMaker Clarify if you need to analyze existing data or predictions for bias and explainability.

### Security & FAQ Insights
- Active learning automatically labels "easy" examples and routes only ambiguous ones to human reviewers (in-house teams, Mechanical Turk, or vendors), reducing labeling cost and time.

### 🔥 Exam Weight
⭐⭐⭐ — Moderate exam topic; useful for recognizing targeted service triggers and common differentiators.

---

## SageMaker Data Wrangler

**Scope:** `BUILD`

### Definition & Purpose
A visual data preparation capability for exploring, cleaning, transforming, and featurizing data before model training with minimal code. The FAQ/exam role is upstream data prep inside the SageMaker lifecycle: transformations can be exported and operationalized so the same preparation logic is reused in training and production pipelines. Standard integrations: **S3**, **SageMaker Feature Store**, **SageMaker Pipelines**, and notebook/Studio workflows; deployment model: fully managed visual data-preparation capability inside SageMaker.

**Official FAQ/reference:** https://aws.amazon.com/sagemaker/ai/faqs/

### Exam Triggers
- "quickly clean, transform, and visualize data before training without custom code"
- "normalize numerical features and one-hot encode categorical columns"

### Not to be confused with (MANDATORY)
SageMaker Feature Store - Data Wrangler is the **upstream** tool used to create and apply transformations, while Feature Store is the **downstream** repository that stores and serves the resulting features for reuse.

▎ Choose SageMaker Data Wrangler if you need visual data cleaning, transformation, encoding, imputation, or feature engineering before training / Choose SageMaker Feature Store if you need to store and serve finalized features consistently for training and inference.

### Security & FAQ Insights
- Exported transformation flows integrate directly with Feature Store and SageMaker Pipelines, allowing transformations to be operationalized and reused across training and inference.

### 🔥 Exam Weight
⭐⭐ — Lower-frequency topic; mainly tested through niche limitations or modern AWS positioning.

---

## SageMaker Feature Store

**Scope:** `BUILD` · `DEPLOY`

### Definition & Purpose
A centralized feature repository for storing, sharing, discovering, and serving curated ML features consistently across training and inference. The FAQ/exam distinction is the dual-store pattern: the **Online Store** supports low-latency real-time feature lookups, while the **Offline Store** stores historical features in S3 for training, batch, and analytics. Standard integrations: **S3**, Data Wrangler/Pipelines, SageMaker training/inference, and analytics tools; deployment model: fully managed online/offline feature repository.

**Official FAQ/reference:** https://aws.amazon.com/sagemaker/ai/faqs/

### Exam Triggers
- "ensure consistency of features between training and inference"
- "avoid training/serving skew"
- "retrieve the latest customer features at inference time while reusing the same features historically for training"

### Not to be confused with (MANDATORY)
SageMaker Data Wrangler - Data Wrangler creates/transforms features upstream, while Feature Store stores and serves the finalized features for both training (Offline Store) and real-time inference (Online Store).

▎ Choose SageMaker Feature Store if you need a centralized online/offline feature repository to prevent training-serving skew / Choose SageMaker Data Wrangler if you need to visually prepare, transform, and engineer raw data before storing features.

### Security & FAQ Insights
- Prevents training/serving skew by ensuring the same feature definitions are used for both training and inference.
- The Offline Store is backed by S3 and integrates with data lakes and analytics tools; the Online Store provides low-latency lookups for real-time inference.

### 🔥 Exam Weight
⭐⭐⭐ — Moderate exam topic; useful for recognizing targeted service triggers and common differentiators.

---

## SageMaker Training Jobs

**Scope:** `TRAIN`

### Definition & Purpose
A managed training mechanism that provisions selected compute, runs built-in algorithms or custom training code/containers, saves model artifacts to S3, and tears down infrastructure after completion. The FAQ highlights SageMaker AI as the build/train/deploy layer for ML and FMs, including managed heavy lifting around containers, frameworks, hardware, and artifacts. Standard integrations: **S3**, **ECR** for BYOC, built-in frameworks such as PyTorch/TensorFlow, **VPC**, **IAM**, **KMS**, and CloudWatch logs/metrics; deployment model: managed ephemeral training infrastructure selected per job.

**Official FAQ/reference:** https://aws.amazon.com/sagemaker/ai/faqs/

### Exam Triggers
- "train on managed, on-demand infrastructure and pay only for training duration"
- "submit a training job that spins up GPU instances and stops automatically"

### Not to be confused with (MANDATORY)
SageMaker HyperPod - Training Jobs provision ephemeral compute for a typical single training job, while HyperPod provides a **persistent, self-healing cluster** purpose-built for very large-scale, long-running distributed training.

▎ Choose SageMaker Training Jobs if you need ephemeral managed compute for a single or typical training job that tears down after completion / Choose SageMaker HyperPod if you need a persistent self-healing cluster for very large distributed foundation-model training.

### Security & FAQ Insights
- You pay only for the training duration; supports built-in algorithms, custom containers (BYOC via ECR), and popular frameworks (PyTorch, TensorFlow, etc.).
- Can run within a VPC with no internet access.

### 🔥 Exam Weight
⭐⭐⭐⭐ — High-value exam topic; commonly appears in scenario questions and service-comparison traps.

---

## SageMaker HyperPod

**Scope:** `TRAIN`

### Definition & Purpose
A purpose-built cluster service for large-scale distributed training of foundation models and other very large ML workloads that run for long periods across many accelerators. The FAQ/exam value is resilience and operational simplification: HyperPod keeps persistent clusters healthy by detecting, replacing, and recovering from hardware failures so training can resume from checkpoints. Standard integrations include SageMaker training workflows, distributed training frameworks, accelerators, shared storage/checkpoints, and VPC/IAM controls; deployment model: dedicated persistent self-healing training clusters.

**Official FAQ/reference:** https://aws.amazon.com/sagemaker/ai/faqs/

### Exam Triggers
- "train a multi-billion-parameter foundation model across hundreds of GPUs with automatic failure recovery"
- "minimize lost training time from hardware failures during long-running training"

### Not to be confused with (MANDATORY)
SageMaker Training Jobs - Training Jobs are ephemeral, single-job compute that tears down after completion, while HyperPod maintains a **persistent** cluster that automatically detects, repairs, and resumes from the last checkpoint after hardware failures.

▎ Choose SageMaker HyperPod if you need persistent, resilient, large-scale distributed training across many accelerators with failure recovery / Choose SageMaker Training Jobs if you need normal ephemeral training infrastructure that starts for one job and stops afterward.

### Security & FAQ Insights
- Automatically detects, repairs, and resumes training from the last checkpoint after hardware failures, minimizing lost training time.
- Targeted at large-scale, long-running distributed training jobs rather than typical single-job training.

### 🔥 Exam Weight
⭐⭐ — Lower-frequency topic; mainly tested through niche limitations or modern AWS positioning.

---

## SageMaker Automatic Model Tuning (AMT)

**Scope:** `TUNE`

### Definition & Purpose
A managed hyperparameter optimization feature that launches and coordinates multiple training jobs to find the configuration that optimizes a chosen objective metric. The FAQ/exam point is automation of model-performance tuning: you define ranges, metrics, and strategy, and SageMaker manages search approaches such as random, Bayesian optimization, and Hyperband. Standard integrations: SageMaker Training Jobs, **S3** training data/artifacts, CloudWatch metrics, IAM roles, and experiment tracking; deployment model: fully managed hyperparameter optimization workflow.

**Official FAQ/reference:** https://aws.amazon.com/sagemaker/ai/faqs/

### Exam Triggers
- "automatically find the best hyperparameters"
- "optimize a target metric like validation accuracy or loss"
- "run many training jobs with different learning rates/batch sizes"

### Not to be confused with (MANDATORY)
SageMaker Clarify - AMT optimizes model **performance** via hyperparameter search, while Clarify evaluates **bias and explainability**; they address entirely different concerns and are never substitutes for each other.

▎ Choose SageMaker Automatic Model Tuning (AMT) if you need to optimize hyperparameters against a target metric such as validation accuracy or loss / Choose SageMaker Clarify if you need to evaluate bias, fairness, or SHAP-based explainability.

### Security & FAQ Insights
- You define the hyperparameter ranges and an objective metric to optimize (e.g., validation accuracy or loss).
- Supports multiple search strategies: random, Bayesian optimization, and hyperband.

### 🔥 Exam Weight
⭐⭐⭐ — Moderate exam topic; useful for recognizing targeted service triggers and common differentiators.

---

## SageMaker Clarify

**Scope:** `EVALUATE`

### Definition & Purpose
A responsible-AI capability that detects bias in datasets before training and in model predictions after training, and explains predictions with feature-attribution methods such as SHAP. The FAQ/exam distinction is that Clarify performs point-in-time fairness and explainability analysis rather than continuous production drift monitoring. Standard integrations: SageMaker Processing/Training/Endpoints, **S3** datasets and reports, Model Cards, Model Monitor workflows, and governance reviews; deployment model: fully managed bias and explainability analysis capability.

**Official FAQ/reference:** https://aws.amazon.com/sagemaker/ai/faqs/

### Exam Triggers
- "check whether a model shows bias across demographic groups"
- "explain why a model produced a specific prediction"
- "SHAP values / feature attribution"

### Not to be confused with (MANDATORY)
SageMaker Model Monitor - Clarify performs **point-in-time** bias/explainability analysis (typically before or right after training), while Model Monitor performs **continuous, live** monitoring of a production endpoint for drift over time.

▎ Choose SageMaker Clarify if you need point-in-time bias analysis, fairness metrics, or SHAP explanations before or after training / Choose SageMaker Model Monitor if you need continuous drift monitoring on a live production endpoint.

### Security & FAQ Insights
- Pre-training bias metrics examine the dataset itself (e.g., class imbalance across groups); post-training metrics examine model predictions.
- Uses SHAP (SHapley Additive exPlanations) values to attribute a prediction to individual input features.

### 🔥 Exam Weight
⭐⭐⭐⭐ — High-value exam topic; commonly appears in scenario questions and service-comparison traps.

---

## SageMaker Model Cards

**Scope:** `GOVERN`

### Definition & Purpose
A governance feature for documenting model purpose, intended use, owners, training data, evaluation metrics, limitations, risks, and responsible-AI considerations in a structured record. The FAQ/exam relevance is auditability: Model Cards can capture or auto-populate SageMaker training/evaluation details and support review workflows for models built inside or outside SageMaker. Standard integrations: SageMaker training/evaluation metadata, Clarify reports, Model Registry, and governance documentation workflows; deployment model: fully managed governance documentation feature.

**Official FAQ/reference:** https://aws.amazon.com/sagemaker/ai/faqs/

### Exam Triggers
- "document a model's intended use and limitations for governance/audit"
- "responsible AI documentation before internal review"

### Not to be confused with (MANDATORY)
SageMaker Model Registry - Model Cards document descriptive, governance-oriented information about a model (intended use, risks), while Model Registry tracks **versioning and the deployment approval workflow**.

▎ Choose SageMaker Model Cards if you need structured responsible-AI documentation such as intended use, risks, metrics, and limitations / Choose SageMaker Model Registry if you need model versioning, lineage, approval status, and promotion workflow.

### Security & FAQ Insights
- Can auto-populate fields such as training job details and evaluation metrics directly from SageMaker.
- Supports both AWS-trained models and models trained outside SageMaker (imported documentation).

### 🔥 Exam Weight
⭐⭐ — Lower-frequency topic; mainly tested through niche limitations or modern AWS positioning.

---

## SageMaker Model Registry

**Scope:** `GOVERN` · `DEPLOY`

### Definition & Purpose
A model catalog for registering model packages, versions, lineage metadata, approval states, and deployment readiness across the ML lifecycle. The FAQ/exam role is controlled promotion: models can move through statuses such as Pending, Approved, or Rejected, enabling CI/CD pipelines to deploy only approved versions. Standard integrations: SageMaker Pipelines, training jobs, endpoints, Model Cards, IAM, and event-driven automation; deployment model: fully managed model catalog and approval workflow.

**Official FAQ/reference:** https://aws.amazon.com/sagemaker/ai/faqs/

### Exam Triggers
- "version models and control promotion to production"
- "approval workflow before a model is deployed"
- "track lineage across model iterations"

### Not to be confused with (MANDATORY)
SageMaker Model Cards - Model Cards capture descriptive governance/responsible-AI documentation, while Model Registry specifically tracks version lineage and deployment **approval status**, which can trigger downstream automation.

▎ Choose SageMaker Model Registry if you need to version models, track lineage, manage approval states, and promote models to deployment / Choose SageMaker Model Cards if you need descriptive governance documentation about intended use, risks, and limitations.

### Security & FAQ Insights
- Groups related model versions into "model groups," making it easy to track lineage across iterations.
- Approval status can trigger downstream automation (e.g., CI/CD pipelines that deploy approved models).

### 🔥 Exam Weight
⭐⭐⭐ — Moderate exam topic; useful for recognizing targeted service triggers and common differentiators.

---

## SageMaker Real-Time Inference

**Scope:** `DEPLOY`

### Definition & Purpose
A persistent SageMaker endpoint that hosts one or more models for synchronous low-latency predictions where callers wait for an immediate response. The FAQ/exam distinction is that real-time endpoints are always provisioned and billed while running, but support production patterns such as autoscaling, variants, multi-model endpoints, and multi-container endpoints. Standard integrations: SageMaker endpoints, **CloudWatch**, **IAM**, **VPC**, **KMS**, autoscaling, and deployment variants; deployment model: dedicated persistent managed endpoints.

**Official FAQ/reference:** https://aws.amazon.com/sagemaker/ai/faqs/

### Exam Triggers
- "interactive predictions with millisecond response times"
- "live API call must return a decision during checkout"

### Not to be confused with (MANDATORY)
Amazon Bedrock - Bedrock provides serverless inference for foundation models via managed APIs with no infrastructure to manage, while Real-Time Inference hosts **your own model** on a dedicated endpoint that is billed continuously.

▎ Choose SageMaker Real-Time Inference if you need synchronous low-latency predictions from your own custom model on a persistent endpoint / Choose Amazon Bedrock if you need serverless access to pre-trained foundation models without managing an endpoint.

### Security & FAQ Insights
- The endpoint runs continuously, so you pay for the underlying instances even when idle.
- Supports auto-scaling and multi-model/multi-container endpoints to serve several models behind one endpoint.

### 🔥 Exam Weight
⭐⭐⭐⭐ — High-value exam topic; commonly appears in scenario questions and service-comparison traps.

---

## SageMaker Asynchronous Inference

**Scope:** `DEPLOY`

### Definition & Purpose
A SageMaker inference option that accepts queued requests, processes them asynchronously, stores results in S3, and can scale the endpoint down to zero when idle. The FAQ/exam limits are the key differentiator: asynchronous inference handles much larger payloads and longer processing times than real-time inference, including payloads up to 1 GB and processing times up to one hour. Standard integrations: **S3** input/output, **Amazon SNS** notifications, autoscaling, IAM, VPC, and CloudWatch; deployment model: managed queued endpoint that can scale to zero.

**Official FAQ/reference:** https://aws.amazon.com/sagemaker/ai/faqs/

### Exam Triggers
- "payloads up to 1 GB"
- "processing times up to one hour"
- "need an endpoint that scales to zero between requests but still queues work"

### Not to be confused with (MANDATORY)
SageMaker Batch Transform - Batch Transform spins up temporary resources for **one bulk job over an entire dataset with no persistent endpoint**, while Asynchronous Inference maintains a **queued endpoint** that scales to zero between individual large-payload requests.

▎ Choose SageMaker Asynchronous Inference if you need an endpoint that queues individual large or long-running requests and can scale to zero when idle / Choose SageMaker Batch Transform if you need a one-off or scheduled bulk scoring job over a full dataset with no persistent endpoint.

### Security & FAQ Insights
- Supports payloads up to 1 GB and processing times of up to one hour, far beyond real-time endpoint limits.
- Results are written to S3 and can trigger notifications via Amazon SNS when complete.

### 🔥 Exam Weight
⭐⭐⭐ — Moderate exam topic; useful for recognizing targeted service triggers and common differentiators.

---

## SageMaker Serverless Inference

**Scope:** `DEPLOY`

### Definition & Purpose
A SageMaker inference option that automatically provisions and scales compute for endpoints with intermittent or unpredictable traffic, including scaling to zero when no requests are active. The FAQ/exam distinction is operational and cost-oriented: serverless inference removes instance management and idle capacity cost, but cold starts make it a poor fit for strict low-latency workloads. Standard integrations: SageMaker endpoints, IAM, CloudWatch, KMS, and model artifacts in S3; deployment model: fully managed serverless endpoint that scales to zero.

**Official FAQ/reference:** https://aws.amazon.com/sagemaker/ai/faqs/

### Exam Triggers
- "intermittent or unpredictable traffic"
- "avoid paying for idle infrastructure"
- "occasional use by an internal tool, cold starts acceptable"

### Not to be confused with (MANDATORY)
SageMaker Asynchronous Inference - Asynchronous Inference targets **large payloads/long processing times** via a queued endpoint, while Serverless Inference targets **small, sporadic, near-real-time requests** with automatic scale-to-zero.

▎ Choose SageMaker Serverless Inference if requests are small, sporadic, latency-tolerant, and should not pay for idle infrastructure / Choose SageMaker Asynchronous Inference if requests are large, long-running, queued, or may take up to an hour.

### Security & FAQ Insights
- Cold starts add latency after idle periods, making it unsuitable for strict low-latency requirements.
- You pay only for the compute used to process requests, not for idle capacity.

### 🔥 Exam Weight
⭐⭐⭐ — Moderate exam topic; useful for recognizing targeted service triggers and common differentiators.

---

## SageMaker Batch Transform

**Scope:** `DEPLOY`

### Definition & Purpose
A batch inference option that provisions temporary compute to score an entire dataset in S3, writes predictions back to S3, and shuts resources down after the job completes. The FAQ/exam point is that Batch Transform creates no persistent endpoint and is designed for offline scoring where predictions are not needed synchronously. Standard integrations: **S3** input/output, SageMaker model artifacts, IAM, VPC, KMS, and CloudWatch logs; deployment model: managed ephemeral batch inference jobs.

**Official FAQ/reference:** https://aws.amazon.com/sagemaker/ai/faqs/

### Exam Triggers
- "score an entire dataset at once"
- "nightly batch job that scores customer records and writes predictions back to S3"
- "no persistent endpoint needed"

### Not to be confused with (MANDATORY)
SageMaker Asynchronous Inference - Asynchronous Inference maintains a **scalable endpoint** that processes individual queued requests, while Batch Transform creates **no endpoint at all** and exists only for the duration of one bulk job.

▎ Choose SageMaker Batch Transform if you need offline scoring of an entire S3 dataset in one bulk job with no persistent endpoint / Choose SageMaker Asynchronous Inference if you need endpoint-based queued processing for individual large or long-running requests over time.

### Security & FAQ Insights
- No persistent endpoint is created or billed - compute exists only for the duration of the job.
- Well suited for scenarios where predictions are not needed in real time (e.g., periodic scoring).

### 🔥 Exam Weight
⭐⭐⭐ — Moderate exam topic; useful for recognizing targeted service triggers and common differentiators.

---

## SageMaker Model Monitor

**Scope:** `MONITOR`

### Definition & Purpose
A continuous production monitoring capability for SageMaker endpoints that compares live inference data and model quality signals against baselines. The FAQ/exam distinction is ongoing monitoring after deployment: Model Monitor detects data quality issues, model quality degradation, bias drift, and feature attribution drift, while surfacing violations through reports and CloudWatch. Standard integrations: SageMaker endpoints, captured inference data in **S3**, baselines, **CloudWatch** metrics/alarms, and Clarify-related bias/explainability checks; deployment model: fully managed continuous production monitoring for SageMaker endpoints.

**Official FAQ/reference:** https://aws.amazon.com/sagemaker/ai/faqs/

### Exam Triggers
- "detect data drift or concept drift in a production model"
- "get a CloudWatch alert when production input data diverges from training data"

### Not to be confused with (MANDATORY)
SageMaker Clarify - Clarify performs point-in-time bias/explainability analysis (often before deployment), while Model Monitor continuously audits a **live production endpoint** over time.

▎ Choose SageMaker Model Monitor if you need continuous monitoring for data drift, concept drift, and production endpoint quality over time / Choose SageMaker Clarify if you need one-time bias, fairness, or explainability analysis.

### Security & FAQ Insights
- Supports both **real-time production endpoints** (continuous monitoring) and **batch transform jobs** (scheduled monitoring); added batch transform support in October 2022.
- Data Drift = the statistical distribution of input data diverges from the training data; Concept Drift = the relationship between inputs and outputs changes over time, degrading prediction accuracy.

### 🔥 Exam Weight
⭐⭐⭐⭐ — High-value exam topic; commonly appears in scenario questions and service-comparison traps.

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
