# Sensing Prompts Analysis

This document lists all the prompt generation blocks and prompt fragments found in `core/llm/prompts/sensing_prompts.py`.

## Prompt Fragments & Reusable Blocks

These functions generate specific textual blocks that are injected into larger prompts to enforce consistent rules across different tasks.

| Function Name | As-Is | Requires Modification| Description |
| :--- | :--- | :--- |:--- |
| **`_experience_memory_block`** | | | Combines experience memory, prompt patches, and feedback into a prompt block. |
| **`_custom_requirements_block`** | | |  Builds a high-priority custom requirements block to guide the LLM's analysis. |
| **`_stale_years_str`** | | | Returns a comma-separated list of years that are considered stale. |
| **`_recency_block_classify`** | | |  Builds the recency rules block for the classifier prompt, enforcing date limits and staleness checks. |
| **`_recency_block_report`** | | |  Builds the recency enforcement block for report-phase prompts. |
| **`_quadrant_names_inline`** | | | Returns quadrant names as a slash-separated inline string. |
| **`_quadrant_definitions_block`** | | |  Returns the definitions of the four radar quadrants (Techniques, Platforms, Tools, Languages & Frameworks). |
| **`_recency_block_domain_intelligence`** | | |  Generates anti-bias rules for the domain-intelligence prompt to prevent training-data bias. |

## Full Prompt Generators

These functions return complete prompt structures (typically lists of `role` and `parts` dictionaries) intended to be sent directly to the LLM.

| Function Name | As-Is | Requires Modification| Description |
| :--- | :--- | :--- | :--- |
| **`sensing_classify_prompt`** | | |  Classifies and summarizes a batch of articles, identifying relevance, topic, and industry segment. |
| **`sensing_report_core_prompt`** | | |  (Phase 1) Generates the executive summary, headline moves, and key trends. |
| **`sensing_report_radar_prompt`** | | |  (Phase 2) Generates technology radar items, ensuring deduplication and recency. |
| **`sensing_report_insights_prompt`** | | |  (Phase 3) Generates deep-dive report sections, recommendations, notable articles, and blind spots. |
| **`sensing_details_prompt`** | | |  Generates detailed write-ups (what it is, why it matters, current state, key players, applications, etc.) for selected technologies. |
| **`sensing_relationship_prompt`** | | |  Classifies relationships between co-occurring technologies (e.g., enables, competes_with). |
| **`sensing_document_topic_extraction_prompt`** | | |  Extracts key topics, technologies, and search queries from a parsed document. |
| **`sensing_domain_intelligence_prompt`** | | |  Generates comprehensive domain intelligence (search queries, keywords, feeds, blocklists) for configuring the pipeline. |
| **`onepager_bullets_prompt`** | | |  Distills top events into concise, factual one-pager cards. |
| **`onepager_custom_topic_prompt`** | | |  Produces one infographic card for a user-requested topic based on web sources. |
| **`onepager_enriched_card_prompt`** | | |  Synthesizes a rich one-pager card from topic/event context and freshly-gathered web sources. |
| **`onepager_verify_card_prompt`** | | |  Acts as a strict fact-checker to strip unsupported metrics, bullets, or people from a draft card. |
| **`model_release_injection_prompt`** | | |  Decides which AI model releases to inject into the report and generates the structured top event and radar items. |
| **`latest_tech_selection_prompt`** | | |  Selects the most consequential new technologies from a tech-sensing report for deep-dive write-ups. |

## External Data Sources

This table lists all external data sources integrated into the TechSensing pipeline for article ingestion and enrichment.

| Data Source | Type | Primary Purpose | Pipeline Stage |
| :--- | :--- | :--- | :--- |
| **RSS Feeds** | Primary Ingestion | Retrieves articles from pre-configured domain-specific blogs, generic tech news, and specific arXiv/Reddit feeds. | Stage 1 (Ingest) |
| **DuckDuckGo Search** | Primary Ingestion / Enrichment | Discovers news articles based on domain intelligence queries. Also used in Stage 7 for funding signal enrichment. | Stage 1 (Ingest) & Stage 7 |
| **GitHub Trending** | Primary Ingestion | Finds emerging open-source projects, tools, and repositories. | Stage 1 (Ingest) |
| **arXiv** | Primary Ingestion | Identifies pre-print academic papers and research breakthroughs. | Stage 1 (Ingest) |
| **HackerNews** | Primary Ingestion | Surfaces community-curated tech news, discussions, and launches. | Stage 1 (Ingest) |
| **Google Patents** | Primary Ingestion | Extracts recent patent filings to evaluate R&D trends and technology readiness. | Stage 1 (Ingest) |
| **Semantic Scholar** | Primary Ingestion | Finds peer-reviewed scientific literature and research papers. | Stage 1 (Ingest) |
| **Reddit** | Primary Ingestion | Captures community signals, weak signals, and discussions from targeted subreddits. | Stage 1 (Ingest) |
| **YouTube** | Enrichment | Finds trending and relevant video content corresponding to identified radar technologies. | Stage 7 (Enrichment) |
| **Artificial Analysis** | Enrichment | Fetches new AI model releases and benchmark data (specifically for GenAI domains). | Stage 7 (Enrichment) |

## External Data Sources URLs

This table specifies the exact API endpoints and base URLs accessed by each data source module in the code.

| Data Source | Full URLs (API Endpoints & Item Links) |
| :--- | :--- |
| **YouTube** | `https://www.googleapis.com/youtube/v3/search`<br>`https://www.googleapis.com/youtube/v3/videos`<br>`https://www.youtube.com/watch?v={video_id}` |
| **Semantic Scholar** | `https://api.semanticscholar.org/graph/v1/paper/search` |
| **arXiv** | `https://export.arxiv.org/api/query` |
| **Reddit** | `https://www.reddit.com/search.json`<br>`https://www.reddit.com{permalink}` |
| **GitHub Trending** | `https://api.github.com/search/repositories` |
| **HackerNews** | `https://hn.algolia.com/api/v1/search_by_date`<br>`https://news.ycombinator.com/item?id={objectID}` |
| **Artificial Analysis & HuggingFace** | `https://artificialanalysis.ai/api/v2/data/llms/models`<br>`https://artificialanalysis.ai/api/v2/data/media/text-to-image`<br>`https://artificialanalysis.ai/api/v2/data/media/text-to-video`<br>`https://artificialanalysis.ai/api/v2/data/media/image-to-video`<br>`https://artificialanalysis.ai/api/v2/data/media/image-editing`<br>`https://artificialanalysis.ai/api/v2/data/media/text-to-speech`<br>`https://artificialanalysis.ai/models/{slug}`<br>`https://huggingface.co/api/models` |
| **Dev.to** | `https://dev.to/api/articles`<br>`https://dev.to/search/feed_content`<br>`https://dev.to{url}` |
| **Google Patents** | Powered by Tavily Search API (`https://tavily.com/`) |
| **DuckDuckGo** | Uses the `duckduckgo_search` library (`https://duckduckgo.com`) |
| **RSS Feeds** | Dynamic per-domain URLs |

## Project Architecture

This diagram illustrates the high-level system architecture, showing the interaction between the frontend, backend services, external data sources, and the LLM engine.

```mermaid
graph TD
    %% Define styles
    classDef frontend fill:#e1f5fe,stroke:#01579b,stroke-width:2px;
    classDef backend fill:#fff3e0,stroke:#e65100,stroke-width:2px;
    classDef core fill:#e8f5e9,stroke:#1b5e20,stroke-width:2px;
    classDef external fill:#f3e5f5,stroke:#4a148c,stroke-width:2px;
    classDef llm fill:#e0f7fa,stroke:#006064,stroke-width:2px;
    classDef storage fill:#efebe9,stroke:#4e342e,stroke-width:2px;
    classDef export fill:#fbe9e7,stroke:#bf360c,stroke-width:2px;

    %% Nodes
    User([User / Frontend Client]):::frontend
    
    subgraph App Layer [FastAPI Application]
        API[REST API Routes]:::backend
        Socket[Socket.IO Handler]:::backend
        Middlewares[Auth & Middlewares]:::backend
        Scheduler[Background Scheduler]:::backend
    end

    subgraph Core Engine [TechSensing Pipeline]
        Orchestrator[Pipeline Orchestrator]:::core
        Ingest[Data Ingestion Module]:::core
        Dedup[Deduplication & Filters]:::core
        Extractor[Full Text Extractor]:::core
        Classifier[Article Classifier]:::core
        ReportGen[Report Generator]:::core
        Verify[Report Verifier & Enhancer]:::core
        Enrichment[Signal & Context Enrichment]:::core
        SelfLearning[Self-Learning & Eval]:::core
    end

    subgraph Integrations & Export
        Dashboard[Web Dashboard]:::export
        Notion[Notion Export]:::export
        Jira[Jira Export]:::export
        Linear[Linear Export]:::export
        Email[Email Digest]:::export
    end

    subgraph Storage & Caching
        Cache[Local/Redis Cache]:::storage
        DomainIntel[Domain Presets & References]:::storage
        Memory[Experience Memory & Prompts]:::storage
        DataDir[Raw Data & Reports Storage]:::storage
    end

    subgraph External Data Sources
        RSS[RSS Feeds]:::external
        DDG[DuckDuckGo Search]:::external
        Github[GitHub Trending]:::external
        Arxiv[arXiv]:::external
        HN[HackerNews]:::external
        Patents[Google Patents]:::external
        SemanticScholar[Semantic Scholar]:::external
        Reddit[Reddit]:::external
        YouTube[YouTube Videos]:::external
        ArtAnalysis[Artificial Analysis / Models]:::external
    end

    subgraph LLM Services
        PromptEngine[Prompt Templates & Assembly]:::llm
        LLM[LLM Provider APIs - OpenAI, Anthropic, etc.]:::llm
    end

    %% Edges
    User -- HTTP Requests --> Middlewares
    User -- Real-time Progress --> Socket
    Middlewares --> API
    API --> Orchestrator
    Scheduler -. Triggers Background Runs .-> Orchestrator
    
    Orchestrator --> DomainIntel
    Orchestrator --> Ingest
    Ingest --> RSS & DDG & Github & Arxiv & HN & Patents & SemanticScholar & Reddit
    Ingest <--> Cache
    
    Ingest --> Dedup
    Dedup --> Classifier
    Classifier --> Extractor
    Extractor --> ReportGen
    ReportGen --> Verify
    Verify --> Enrichment
    Enrichment --> YouTube & ArtAnalysis
    Enrichment --> SelfLearning
    
    SelfLearning --> Memory
    Orchestrator --> Memory
    
    Classifier <--> PromptEngine
    ReportGen <--> PromptEngine
    Verify <--> PromptEngine
    Enrichment <--> PromptEngine
    SelfLearning <--> PromptEngine
    PromptEngine <--> LLM
    
    Orchestrator --> DataDir
    Orchestrator --> Dashboard & Notion & Jira & Linear & Email
```

## Data and Function Flow

This diagram traces the execution path of the `run_sensing_pipeline` function in precise detail, representing the lifecycle of a single TechSensing run.

```mermaid
graph TD
    classDef stage fill:#ede7f6,stroke:#4527a0,stroke-width:2px;
    classDef subtask fill:#fafafa,stroke:#9e9e9e,stroke-width:1px;
    classDef llmtask fill:#e0f7fa,stroke:#00838f,stroke-width:1px;

    Start([Start Pipeline Run]) --> S0

    S0[Stage 0: Domain & Context]:::stage
    S0 -. LLM/Storage .-> DomainRef[Generate/Load Domain Reference]:::llmtask
    S0 -. Storage .-> UserPrefs[Load Topic Preferences & Org Context]:::subtask
    S0 -. Storage .-> ExpMem[Load Experience Memory & Prompt Patches]:::subtask
    DomainRef & UserPrefs & ExpMem --> S1

    S1[Stage 1: Ingest Data]:::stage
    S1 -. Fetch in Parallel .-> Sources[RSS, DDG, GitHub, arXiv, HN, Patents, S2, Reddit, China Sources]:::subtask
    Sources --> S2

    S2[Stage 2: Deduplicate & Pre-Filter]:::stage
    S2 -. Remove duplicates .-> Dedup[Exact & Fuzzy Deduplication]:::subtask
    Dedup --> KeywordFilter[Keyword Must/Dont Include Filter]:::subtask
    KeywordFilter --> DateFilter[Lookback Date Window Filter]:::subtask
    DateFilter --> S3

    S3[Stage 3: Classify Articles]:::stage
    S3 -. LLM Assessment .-> LLMClassify[LLM: Assess Relevance, Topic, Segment]:::llmtask
    LLMClassify --> BoostFilter[Boost Focus Keywords & Post-Classify Date Filter]:::subtask
    BoostFilter --> S4

    S4[Stage 4: Extract Full Text]:::stage
    S4 -. Scrape Content .-> TextExtraction[Extract Full Article Text]:::subtask
    TextExtraction --> ReconDate[Reconcile Dates & Hard Date Filter]:::subtask
    ReconDate --> S5

    S5[Stage 5: Generate Report]:::stage
    S5 -. LLM Generation .-> CoreReport[LLM: Exec Summary, Top Events, Trends, Radar Items]:::llmtask
    CoreReport --> S6

    S6[Stage 6: Verify & Enhance]:::stage
    S6 -. LLM Validation .-> Verifier[LLM: Verify alignment & Drop hallucinations]:::llmtask
    Verifier -. LLM Gap Fill .-> Enhancer[LLM: Second-pass Gap Filling]:::llmtask
    Enhancer --> S7

    S7[Stage 7: Enrichment & Signals]:::stage
    S7 -. Detect Changes .-> Movement[Radar Movements]:::subtask
    S7 -. Analytics .-> Scoring[Signal Strength & Lifecycle Detection]:::subtask
    S7 -. Context .-> Funding[Funding Signals]:::subtask
    S7 -. Context .-> Relationships[Extract Tech Relationships]:::llmtask
    S7 -. Analytics .-> Confidence[Confidence Scoring & Source Tiers]:::subtask
    S7 -. Advanced .-> WeakSignals[Detect Weak Signals]:::llmtask
    S7 -. Media .-> Videos[Fetch YouTube Trending Videos]:::subtask
    S7 -. GenAI Only .-> ModelReleases[Fetch & Inject Model Releases]:::llmtask
    S7 -. Region .-> ChinaFocus[Generate China Focus Section]:::llmtask
    
    Movement & Scoring & Funding & Relationships & Confidence & WeakSignals & Videos & ModelReleases & ChinaFocus --> S8

    S8[Post-Processing: Self-Learning]:::stage
    S8 -. LLM Evaluation .-> SelfEval[LLM: Evaluate Report Quality]:::llmtask
    SelfEval -. Storage .-> MemUpdate[Save Run Summary to Disk]:::subtask
    MemUpdate -. LLM Evolution .-> PromptEvolve[Maybe Evolve Prompt Patches]:::llmtask
    PromptEvolve --> End([End: Report Ready])
```
