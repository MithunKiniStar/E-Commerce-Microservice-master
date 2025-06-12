Why This Solution Is Useful
Our organization manages complex code repositories with multiple microservices, which often overwhelm new developers joining a project. Without comprehensive documentation, these developers rely heavily on peers and seniors for code assistance, leading to delays, knowledge silos, and inefficiencies. As our codebase grows with each sprint, manually updating documentation becomes a tedious, error-prone task that is frequently deprioritized. This creates a cycle of outdated or incomplete documentation, further complicating onboarding and knowledge sharing.

Imagine a solution where an AI-powered LLM tool automatically reads and analyzes our Bitbucket / GitHub repositories, generating clear, beginner-friendly tutorials and documentation in real time. This tool, inspired by the Pocket Flow Project, would crawl our repositories, identify core abstractions, and produce structured Markdown tutorials complete with explanations, analogies, and visualizations like Mermaid flowcharts. These tutorials would not only accelerate onboarding new developers but also serve as a living knowledge base, enabling developers to navigate complex codebases with ease.

Moreover, integrating our tool across all projects ensures that each project benefits from standardized and detailed documentation. This comprehensive approach empowers developers to find solutions to common problems without reinventing the wheel. For instance, if a developer needs to implement Timeseries Elasticsearch, they can instantly access relevant documentation and code references from other projects within the organization. Instead of posting questions on platforms like StackOverflow or waiting for responses, developers will have immediate access to a centralized, AI-generated knowledge base enriched with context-specific code snippets and best practices. Looking ahead, we envision expanding this system to include a centralized code buddy, leveraging these detailed documentations as a source to answer any code-based implementation queries within our organization. This future capability will further reduce redundant efforts, foster cross-team collaboration, and accelerate problem-solving.

Additional benefits include:

Consistency Across Teams: Standardized documentation ensures all teams follow the same conventions, improving code quality and maintainability.
Knowledge Retention: As team members leave or transition, the documentation preserves institutional knowledge, reducing the impact of turnover.
Scalability: The solution scales effortlessly across repositories, supporting our growing portfolio of projects without additional manual effort.


By leveraging Bitbucket / GitHub’s Webhook, API and a lightweight LLM framework, this tool integrates seamlessly into our existing workflows, transforming how we document, share, and leverage our codebases.

 Idea
We propose developing an AI-powered tool that automatically analyzes Bitbucket / GitHub repositories and generates beginner-friendly tutorials and documentation for complex codebases. Adapted from the Pocket Flow Project, this solution will crawl Bitbucket / GitHub repositories, build a knowledge base, identify up to 10 core abstractions, and transform them into digestible tutorials with clear explanations, analogies, and visualizations. Unlike the original GitHub-focused project, our tool will integrate with Bitbucket / GitHub's API and authentication, aligning with our organization’s infrastructure and trained to generate documents as per firm's documentation standards. The doc tutorials will accelerate the developer onboarding, reduce the learning curve, improve codebase maintainability, and enable cross-project knowledge sharing.

Kindly Note: We have successfully completed the proof of concept (POC) and are currently in the final stages of testing with multiple codebases. Our goal is to enhance the tool's robustness and configurability, ensuring seamless integration with any project within the firm.

The tool will:

Crawl Bitbucket / GitHub repositories or local directories to fetch code.
Use an LLM (leveraging model from firm's AWS Bedrock or Azure AI) to analyze code, identify key abstractions, and describe their relationships.
Generate tutorials tailored for beginners, including visualizations like Mermaid flowcharts.
Output tutorials as Markdown files, organized into chapters, stored in a designated Bitbucket / GitHub repository or local directory. Please refer the following link and the Appendix section for the sample of AI generated documents based on the code stored in GitHub.
GitHub Link for AI generated Code Docs : https://github.com/MithunKiniStar/E-Commerce-Microservice-master/blob/main/output/02_api_gateway___dynamic_routing_.md#the-request-journey-a-step-by-step-example

# Technical Architecture Documentation: Tutorial Codebase Knowledge System

## System Overview

The Tutorial Codebase Knowledge system is an advanced documentation generation platform that leverages LLMs (Large Language Models) to automatically analyze, understand, and document codebases. The system employs a modular architecture with distinct processing pipelines to generate comprehensive, maintainable documentation.

## Architecture Overview
The system follows a three-layer architecture designed for efficient documentation generation. The Input Layer accepts code from either local directories or Git repositories and processes them through a unified Input Processor. The Core Processing Layer contains three main engines: the Code Analysis Engine for understanding code structure and patterns, the LLM Processing Pipeline for intelligent content analysis and abstraction, and the Documentation Generator for organizing and creating coherent documentation. Finally, the Output Layer generates both markdown content and diagrams, storing them in a documentation repository, which is then stored as AI-generated documentation in the output folder of the Bitbucket repository of a particular project with an automatically updated README for easy navigation.

```mermaid
graph TB
    subgraph Input Layer
        A1[Local Directory] --> P1
        A2[Git Repository] --> P1
        P1[Input Processor]
    end

    subgraph Core Processing Layer
        P1 --> B1
        subgraph "Code Analysis Engine"
            B1[File Crawler] --> B2[Pattern Analyzer]
            B2 --> B3[Dependency Mapper]
        end
        
        B3 --> C1
        subgraph "LLM Processing Pipeline"
            C1[Context Builder] --> C2[Abstraction Extractor]
            C2 --> C3[Relationship Analyzer]
        end
        
        C3 --> D1
        subgraph "Documentation Generator"
            D1[Content Organizer] --> D2[Chapter Generator]
            D2 --> D3[Cross-Referencer]
        end
    end

    subgraph Output Layer
    D3 --> E1[Markdown Generator]
    D3 --> E2[Diagram Generator]
    E1 --> F1[Documentation Repository]
    E2 --> F1
    F1 --> G1[Store AI Generated Docs in Output Folder of Bitbucket Repo]
    G1 --> G2[Update Repository README]
end
```

## Component Details

### 1. Input Processing Layer
The Input Processing Layer serves as the system's entry point, providing a flexible and robust mechanism for ingesting code from multiple sources. It implements a dual-channel approach, handling both local directory scanning and Git repository processing through a unified InputProcessor interface. The layer incorporates sophisticated file filtering capabilities through the FileFilter class, which enforces configurable rules for file inclusion, exclusion, and size limitations. This ensures that only relevant code files are processed while maintaining system performance and security. The layer's modular design allows for easy extension to support additional input sources while maintaining consistent validation and preprocessing across all input channels.

```mermaid
classDiagram
    class InputProcessor {
        +process_local_dir(path: str)
        +process_git_repo(url: str)
        +validate_input()
        -filter_files()
    }
    class FileFilter {
        +include_patterns: Set
        +exclude_patterns: Set
        +max_file_size: int
        +apply_filters()
    }
    InputProcessor --> FileFilter
```

#### Implementation Details
- **File Crawling**: `utils/crawl_local_files.py`
  - Configurable file patterns
  - Size limitations
  - Exclusion patterns
- **Git Integration**: `utils/crawl_git_files.py`
  - Repository cloning
  - Branch management
  - Access token handling

### 2. Code Analysis Engine

The Code Analysis Engine forms the analytical backbone of the system, employing a sophisticated three-component architecture to comprehensively understand codebases. The FileAnalyzer serves as the primary entry point, coordinating the analysis process by examining code structure, identifying patterns, and mapping dependencies. It works in conjunction with the PatternMatcher, which leverages a comprehensive dictionary of language-specific patterns to identify architectural styles and design patterns across different programming languages. The DependencyGraph component constructs and analyzes relationship graphs between code elements, providing crucial insights into code organization and dependencies. Together, these components enable deep, language-agnostic analysis of code structure and relationships, forming the foundation for intelligent documentation generation.

```mermaid
classDiagram
    class FileAnalyzer {
        +analyze_structure()
        +detect_patterns()
        +extract_dependencies()
    }
    class PatternMatcher {
        +language_patterns: Dict
        +match_patterns()
        +identify_architecture()
    }
    class DependencyGraph {
        +nodes: List
        +edges: List
        +build_graph()
        +analyze_relationships()
    }
    FileAnalyzer --> PatternMatcher
    FileAnalyzer --> DependencyGraph
```

#### Key Features
- Language-agnostic analysis
- Design pattern recognition
- Dependency mapping
- Architecture identification

### 3. LLM Processing Pipeline
The LLM Processing Engine serves as the intelligent core of the documentation system, transforming raw code analysis into meaningful documentation. It employs a sophisticated pipeline where the Context Builder first aggregates and structures code analysis results, creating rich contextual prompts that capture the essence of the codebase. These prompts are then processed by the LLM service (supporting multiple providers like Gemini, OpenAI, and Anthropic) which generates human-readable, technically accurate documentation. The engine includes built-in mechanisms for caching responses, handling rate limits, and recovering from errors, ensuring reliable and efficient documentation
```mermaid
sequenceDiagram
    participant CA as Code Analyzer
    participant CB as Context Builder
    participant LLM as LLM Service
    participant DG as Doc Generator
    
    CA->>CB: Send Code Analysis
    CB->>CB: Build Context
    CB->>LLM: Generate Prompts
    LLM->>LLM: Process Content
    LLM->>DG: Return Structured Output
    DG->>DG: Format Documentation
```

#### Implementation Details
- **Context Building**: `nodes.py`
  ```python
  class ContextBuilder(Node):
      def prep(self, shared):
          return self.build_context(shared["files"])
      
      def exec(self, context):
          return self.process_with_llm(context)
  ```

- **LLM Integration**: `utils/call_llm.py`
  - Multiple provider support (Gemini, OpenAI, Anthropic)
  - Caching mechanism
  - Rate limiting handling
  - Error recovery

 


### 4. Documentation Generator
The Documentation Generator is the final processing stage that transforms the LLM-generated content into well-structured, navigable documentation. It begins with Content Organization, which logically arranges the generated content based on relationships and importance. The system then applies appropriate templates through Template Selection, ensuring consistent formatting and presentation. During Content Generation, it assembles the documentation while maintaining technical accuracy and readability. The Cross-Reference Creation phase establishes intelligent links between related sections, enhancing navigation and understanding. Finally, Format Conversion transforms the content into various output formats, supporting both markdown and diagram generation for comprehensive documentation delivery.

```mermaid
graph LR
    A[Content Organization] --> B[Template Selection]
    B --> C[Content Generation]
    C --> D[Cross-Reference Creation]
    D --> E[Format Conversion]
```

#### Output Structure
```
project_docs/
├── index.md                 # Main entry point
├── architecture/
│   ├── overview.md         # System overview
│   ├── components.md       # Component details
│   └── patterns.md         # Design patterns
├── implementation/
│   ├── setup.md           # Setup instructions
│   ├── configuration.md   # Configuration details
│   └── examples.md        # Usage examples
└── api/
    ├── interfaces.md      # API documentation
    └── integration.md     # Integration guides
```

## Data Flow
### 1. Input Processing
The Input Processing data flow implements a streamlined approach to handle diverse code sources. Starting with Raw Input, the system first determines the Input Type, branching into two specialized processing paths: the Directory Scanner for local filesystem inputs and the Repository Fetcher for Git-based sources. Both paths converge at the File Filter stage, which applies consistent filtering rules (file types, sizes, and exclusion patterns) across all inputs. This unified filtering approach ensures that regardless of the input source, the system produces a standardized set of Processed Files ready for subsequent analysis. The design emphasizes both flexibility in input handling and consistency in output, making the system adaptable while maintaining reliable processing standards.
```mermaid
flowchart LR
    A[Raw Input] --> B{Input Type}
    B -->|Local| C[Directory Scanner]
    B -->|Git| D[Bitbucket Repository Fetcher]
    C --> E[File Filter]
    D --> E
    E --> F[Processed Files]
```

### 2. Analysis Pipeline
The Analysis Pipeline implements a sophisticated sequential processing flow that progressively builds understanding of the codebase. Beginning with Processed Files from the input stage, the pipeline first performs Code Analysis to understand the basic structure and syntax. Pattern Detection then identifies common design patterns and architectural styles within the code. The Dependency Mapping stage constructs a comprehensive graph of relationships between code components. Context Generation aggregates these insights to create rich, contextual information about the codebase. Finally, LLM Processing uses this context to generate intelligent, human-readable documentation. This staged approach ensures thorough analysis while maintaining efficient processing, with each stage building upon the insights gained from previous stages.

```mermaid
flowchart LR
    A[Processed Files] --> B[Code Analysis]
    B --> C[Pattern Detection]
    C --> D[Dependency Mapping]
    D --> E[Context Generation]
    E --> F[LLM Processing]
```

## Implementation Guidelines

### 1. Configuration Management
```yaml
# config.yaml
input:
  include_patterns:
    - "*.py"
    - "*.java"
    - "*.js"
  exclude_patterns:
    - "test/*"
    - "docs/*"
  max_file_size: 100000

llm:
  provider: "gemini"
  model: "gemini-2.5-pro-preview-03-25"
  temperature: 0.7
  
output:
  format: "markdown"
  diagrams: true
  cross_references: true
```

### 2. Error Handling
```python
class ProcessingNode(Node):
    def exec_fallback(self, prep_res, exc):
        logger.error(f"Processing failed: {exc}")
        return {
            "status": "error",
            "message": str(exc),
            "fallback_content": self.generate_fallback(prep_res)
        }
```

### 3. Performance Optimization
- Caching strategy for LLM calls
- Parallel processing for file analysis
- Incremental documentation updates
- Memory-efficient file processing

## Security Considerations

### 1. API Key Management
- Environment variable usage
- Key rotation policies
- Access scope limitations

### 2. Code Analysis Security
- File size limitations
- Content validation
- Execution prevention

### 3. Output Validation
- Content sanitization
- Reference verification
- Format validation

## Monitoring and Maintenance

### 1. Logging
```python
# Structured logging implementation
logging.config.dictConfig({
    'version': 1,
    'handlers': {
        'console': {
            'class': 'logging.StreamHandler',
            'formatter': 'standard'
        },
        'file': {
            'class': 'logging.FileHandler',
            'filename': 'processing.log',
            'formatter': 'detailed'
        }
    },
    'formatters': {
        'standard': {
            'format': '%(asctime)s - %(name)s - %(levelname)s - %(message)s'
        },
        'detailed': {
            'format': '%(asctime)s - %(name)s - %(levelname)s - %(message)s - %(context)s'
        }
    }
})
```

### 2. Metrics
- Processing time per file
- LLM token usage
- Error rates
- Documentation coverage

## Deployment Architecture

```mermaid
graph TB
    subgraph "Development Environment"
        A1[Local Setup] --> B1[Git Repository]
    end
    
    subgraph "CI/CD Pipeline"
        B1 --> C1[Build Process]
        C1 --> D1[Testing]
        D1 --> E1[Documentation Generation]
    end
    
    subgraph "Production Environment"
        E1 --> F1[Static Site Generator]
        F1 --> G1[Hosted Documentation]
    end
```

## Integration Points

### 1. Version Control
- Git repository integration
- Branch-specific documentation
- Version tagging

### 2. CI/CD Integration
- Automated documentation updates
- Quality checks
- Deployment automation

### 3. External Tools
- IDE plugins
- Documentation platforms
- Collaboration tools

## Future Enhancements

### 1. Planned Features
- Real-time documentation updates
- Multi-language support expansion
- Interactive documentation features
- Advanced visualization options

### 2. Scalability Improvements
- Distributed processing
- Cloud integration
- Performance optimization

## Conclusion

This technical architecture provides a robust foundation for automated documentation generation, leveraging modern AI capabilities while maintaining flexibility and extensibility. The system's modular design allows for easy updates and enhancements while ensuring reliable documentation output. 
