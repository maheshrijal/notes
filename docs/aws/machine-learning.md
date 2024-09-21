---
title: AWS Machine Learning
date: 2024-04-02
---

# AWS Machine Learning

## Amazon Comprehend

- Natural Language Processing (NLP)
- Input = Document
- Output = Entities, phrases, language, PII, sentiments
- Pre-trained models or custom
- Real-time analysis for small workloads
- Async jobs for larger workloads
- Console, CLI for interactive or use APIs to build into applications

## Amazon Kendra

- Intelligent search service designed to mimic interacting with human expert
- Supports wide range of question types
- Factoid: Who, What & Where
- Descriptive: How do I ..?
- Keyword: What day is the keynote address?
- Index: searchable data organized in an efficient way
- Data source: Where your data lives. Kendra connects and indexes from this location
- Synchronize with index based on a schedule
- Integrates with AWS Services (IAM, Identity Center)

## Amazon Lex

- Text or Voice consersational interfaces
- Powers the **Alexa service**
- Automatic speech recognition (ASR) - speech to text
- Natural Language Understanding (NLU) - Intent
- Build understanding into you application
- Scales, integrates, Quick to deploy, pay as you go pricing
- Chatbots, voice assistants, Q&A bots, Info/Enterprise Bots
- Lex can fulfill the intent using lambda integration

## Amazon Polly

- Converts text into "life-like" speech
- Text (language) => Speech (language) NO TRANSLATION
- Modes: Standard TTS
- Modes: Neural TTS
- Can use Speech Synthesis Markup Language (SSML): Additional control over how polly generates speech (emphasis, pronunciation, whispering)

## Amazon Rekognition

- Deep learning image & video analysis
- Identify object, people, text, activities, content moderation, face detection, face analysis, face comparison, pathing & much more
- Pay per image or per minute video
- Integrates with application & event driven
- Can analyse live video streams - kinesis video streams

## Amazon Textract

- Detect and Analyse text contained in input documents
- Input = JPEG, PNG, PDF or TIFF
- Output = Extracted text, structure & Analysis
- Synchronous (real-time) for small documents and Asynchronous for large files
- Pay per usage. Custom pricing available for large volume
- Use case:
    - Detection of text & relationship between text
    - generate metadata
    - Document analsysis (names,addresses, birthdate)
    - Receipt analysis (prices, vendor, line-items, dates)
    - Identity documents (document ID)

## Amazon Transcribe

- Automatic Speech Recognition (ASR) service
- Input=Audio, Output=Text
- Language customization, filters for privacy, audience-appropriation language, speaker identification
- Custom vocabularies and language models
- Pay as you use per second of transcribed audio.
- Use case:
    - Full text indexing of audio - allow searching
    - Meeting notes
    - Subtitles/Captions & transcripts
    - Call analytics(characteristics, summarization, categories and sentiment)
    - Integration wiht other apps / AWS ML services

## Amazon Translate

- Text translation service based on ML
- Translates text from native language to other languages. One word at a time.
- Encoder reads source => semantic representation (meaning)
- Decoder reads meaning => writes target language
- **Attention mechanism** ensures 'meaning' is translated
- Auto detect source text language
- Use case:
    - Multilingual user experience
    - Translate incoming data (social media, news, communications)
    - Language independence for other AWS services (Comprehend, transcribe, polly, data stored in S3, RDS, Database)

## Amazon Forecast

- Forecasting for time-series data
- Retail demand, supply chain, staffing, energy, server capacity, web traffic
- Import historical & related data, Output forecast and forecast explainability
- Web Console (visualization), CLI, APIs, Python SDK

## Amazon Fraud Detector

- Fully managed Fraud detection service
- New account creations, payments, guest checkout
- Upload historical data, choose model type
- Model Types:
    - **Online Fraud** - Little historical data eg: new customer account
    - **Transaction Fraud** - transactional history, idenitfying suspect payments
    - **Acount Takeover** - Identify phishing or another social based attack
- All events are scored. Rules/Detection logic allow you to react to a score based on business activity

## Amazon SageMaker

- Fully managed Machine Learning (ML) service
- Fetch, Clean, Prepare, Train, Evaluate, Deploy, Monitor/Collect
- Sage Maker Studio: Build, train, debug and monitor models - IDE for ML lifecycle
- **Sagemaker Domain** - EFS Volume, Users, Apps, Policies, VPCs -- isolation
- **Container** - Docker containers deployed to ML EC2 instance - ML environments (OS, libraries, tooling)
- **Hosting** - Deploy endpoints for your models
- Sagemaker has no cost - the resources it creates have a cost (Complex pricing)