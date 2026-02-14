# OnboardAI - Technical Design Specification
**AI for Learning & Developer Productivity**

## 1. System Architecture Overview

### 1.1 High-Level Architecture (5-Layer Design)

OnboardAI's architecture is optimized for **learning effectiveness** and **developer productivity**:

```
┌──────────────────────────────────────────────────────────────────┐
│                         USER LAYER                                │
│              (Developers in Learning Mode)                        │
│  ┌────────────────────────────────────────────────────────────┐  │
│  │  1. Developer uploads GitHub repo to learn                 │  │
│  │  2. Developer asks questions about code                    │  │
│  │  3. Developer follows guided learning roadmap              │  │
│  └────────────────────────────────────────────────────────────┘  │
└────────────────────────────┬─────────────────────────────────────┘
                             │
┌────────────────────────────▼─────────────────────────────────────┐
│                    PRESENTATION LAYER                             │
│                (Learning-Optimized UI - Next.js)                  │
│  ┌──────────────┬──────────────┬──────────────┬──────────────┐  │
│  │ 1. Repo URL  │ 2. Learning  │ 3. Summary   │ 4. Diagram   │  │
│  │    Input     │  Chat (Q&A)  │  Dashboard   │   Viewer     │  │
│  │              │  Interface   │              │  (Mermaid)   │  │
│  └──────────────┴──────────────┴──────────────┴──────────────┘  │
│  ┌──────────────────────────────────────────────────────────┐   │
│  │ 5. Guided Learning Roadmap (Phase 1 → 2 → 3)            │   │
│  └──────────────────────────────────────────────────────────┘   │
└────────────────────────────┬─────────────────────────────────────┘
                             │ REST API / GraphQL
┌────────────────────────────▼─────────────────────────────────────┐
│                    APPLICATION LAYER                              │
│                (Learning Orchestration - FastAPI)                 │
│  ┌──────────────────────────────────────────────────────────┐    │
│  │  1. API Gateway (FastAPI)                                │    │
│  │  2. Repo Ingestion Module                                │    │
│  │     • GitHub API Fetch                                   │    │
│  │     • Repository Cloning                                 │    │
│  │     • Smart File Filtering (learning-relevant only)      │    │
│  │  3. Query Controller                                     │    │
│  │     • Intent Detection (factual/explanation/visual)      │    │
│  │     • Learning Context Management                        │    │
│  │  4. Response Formatter                                   │    │
│  │     • Educational Explanation Formatting                 │    │
│  │     • Code Snippet Highlighting                          │    │
│  │     • Learning Path Suggestions                          │    │
│  └──────────────────────────────────────────────────────────┘    │
└────────────────────────────┬─────────────────────────────────────┘
                             │
┌────────────────────────────▼─────────────────────────────────────┐
│                    INTELLIGENCE LAYER                             │
│                (Teaching-Optimized Analysis)                      │
│                                                                    │
│  ┌──────────────┬──────────────┬──────────────┬──────────────┐  │
│  │ Static Code  │ Knowledge    │ Retrieval    │ AI Reasoning │  │
│  │  Analyzer    │  Builder     │   Engine     │   Engine     │  │
│  │              │              │              │              │  │
│  │ • AST Parse  │ • Codebase   │ • Keyword    │ • LLM        │  │
│  │ • Function/  │   Mapping    │   Search     │   (Gemini    │  │
│  │   Class      │ • Learning   │ • Metadata   │   Flash)     │  │
│  │   Extract    │   Path Gen.  │   Search     │ • Educational│  │
│  │ • Import &   │ • Pattern    │ • Vector     │   Prompts    │  │
│  │   Dependency │   Detection  │   Search     │ • Mermaid    │  │
│  │   Mapping    │   (Auth, DB  │   (FAISS/    │   Diagram    │  │
│  │ • Entry Point│   MVC, etc.) │   Chroma)    │   Generation │  │
│  │   Discovery  │ • Roadmap    │              │ • Concept    │  │
│  │ • Route      │   Builder    │              │   Teaching   │  │
│  │   Detection  │              │              │              │  │
│  └──────────────┴──────────────┴──────────────┴──────────────┘  │
└────────────────────────────┬─────────────────────────────────────┘
                             │
┌────────────────────────────▼─────────────────────────────────────┐
│                         DATA LAYER                                │
│               (Learning-Optimized Storage)                        │
│  ┌──────────────┬──────────────┬──────────────┬──────────────┐  │
│  │ 1. Temporary │ 2. Knowledge │ 3. Vector DB │ 4. Response  │  │
│  │    Repo      │    Graph     │   (Optional) │    Cache     │  │
│  │    Storage   │    (JSON/    │   FAISS for  │  (Reduce LLM │  │
│  │    (S3/Local)│    Database) │   Semantic   │   API Calls) │  │
│  │              │              │   Search     │              │  │
│  └──────────────┴──────────────┴──────────────┴──────────────┘  │
└──────────────────────────────────────────────────────────────────┘
```

### 1.2 Architecture Principles (Learning-Focused)

1. **Learning-First Design**: Every component optimized for educational value
2. **Fast Feedback Loops**: <60 sec analysis, <10 sec answers (maintain learning flow)
3. **Progressive Disclosure**: Simple explanations first, details on request
4. **Visual-First**: Diagrams generated automatically for better comprehension
5. **Beginner-Friendly**: Explanations use clear language, define jargon
6. **Serverless & Scalable**: AWS Lambda for cost-effectiveness and auto-scaling

---

## 2. Presentation Layer (Learning Interface)

### 2.1 Technology Stack
- **Framework**: Next.js 14 (React + SSR)
- **Language**: TypeScript (type safety)
- **Styling**: Tailwind CSS (rapid UI development)
- **State Management**: Zustand (lightweight, learning-friendly)
- **Code Highlighting**: `react-syntax-highlighter` + Prism
- **Diagram Rendering**: `mermaid.js`, `react-mermaid`
- **Deployment**: Vercel (zero-config Next.js deployment)

### 2.2 Component Architecture

```
src/
├── components/
│   ├── RepoInput/
│   │   ├── RepoInput.tsx           # GitHub URL input
│   │   ├── URLValidator.tsx         # Validate GitHub URLs
│   │   └── AnalysisProgress.tsx     # Loading states
│   │
│   ├── LearningChat/               # Main Q&A interface
│   │   ├── ChatInterface.tsx
│   │   ├── MessageList.tsx
│   │   ├── MessageInput.tsx
│   │   ├── SuggestedQuestions.tsx   # Learning prompts
│   │   └── CodeSnippet.tsx          # Syntax-highlighted code
│   │
│   ├── LearningRoadmap/            # Guided learning path
│   │   ├── RoadmapView.tsx
│   │   ├── PhaseCard.tsx            # Learning phase
│   │   ├── LearningCheckpoint.tsx   # Progress indicators
│   │   └── EstimatedTime.tsx        # Time estimates
│   │
│   ├── ArchitectureDashboard/
│   │   ├── SummaryView.tsx
│   │   ├── TechStackBadges.tsx      # Visual tech stack
│   │   ├── ComponentMap.tsx         # System overview
│   │   └── QuickReference.tsx       # API endpoints, env vars
│   │
│   ├── DiagramViewer/
│   │   ├── MermaidRenderer.tsx      # Render diagrams
│   │   ├── DiagramControls.tsx      # Zoom, pan, export
│   │   └── DiagramGallery.tsx       # Multiple diagram views
│   │
│   └── LearningAids/
│       ├── GlossaryTooltip.tsx      # Define technical terms
│       ├── RelatedConcepts.tsx      # "Learn next" suggestions
│       └── ExternalResources.tsx    # MDN, docs links
│
├── services/
│   ├── api.ts                       # API client
│   ├── repoService.ts               # Repo analysis calls
│   ├── queryService.ts              # Q&A calls
│   └── learningService.ts           # Roadmap, progress
│
├── stores/
│   ├── repoStore.ts                 # Current repository state
│   ├── chatStore.ts                 # Learning conversation
│   └── learningStore.ts             # Learning progress
│
├── types/
│   ├── Repository.ts
│   ├── LearningMessage.ts
│   ├── LearningRoadmap.ts
│   └── CodePattern.ts
│
└── utils/
    ├── codeFormatter.ts
    ├── diagramGenerator.ts
    └── learningPathBuilder.ts
```

### 2.3 Key Learning UI Components

#### 2.3.1 Learning Chat Interface

```typescript
interface LearningMessage {
  role: 'user' | 'assistant';
  content: string;
  codeReferences?: CodeReference[];
  diagrams?: string[];  // Mermaid syntax
  learningTakeaways?: string[];  // Key concepts
  suggestedNext?: string[];  // Related questions
}

const LearningChat: React.FC = () => {
  const [messages, setMessages] = useState<LearningMessage[]>([]);
  
  const suggestedQuestions = [
    "Where should I start learning this codebase?",
    "How does authentication work?",
    "Show me the main data models",
    "Draw the request flow diagram",
    "What design patterns are used?",
  ];
  
  return (
    <div className="learning-chat">
      <MessageList messages={messages} />
      
      {/* Suggested Learning Questions */}
      <SuggestedQuestions
        questions={suggestedQuestions}
        onSelect={(q) => askQuestion(q)}
      />
      
      {/* Input with hints */}
      <MessageInput
        placeholder="Ask a question to learn... (e.g., 'How does login work?')"
        onSend={handleQuestion}
      />
    </div>
  );
};
```

#### 2.3.2 Guided Learning Roadmap

```typescript
interface LearningPhase {
  phase: number;
  title: string;
  description: string;
  estimatedTime: string;  // "2 hours"
  topics: LearningTopic[];
  checkpoint: string;  // What learner should understand after
}

interface LearningTopic {
  title: string;
  files: string[];
  concepts: string[];
  handsOnTasks?: string[];
}

const LearningRoadmap: React.FC<{ roadmap: LearningPhase[] }> = ({ roadmap }) => {
  return (
    <div className="roadmap-container">
      <h2>Your Learning Path</h2>
      <p className="total-time">Estimated: 8 hours total</p>
      
      {roadmap.map((phase, idx) => (
        <PhaseCard key={idx} phase={phase} />
      ))}
      
      <LearningCheckpoints roadmap={roadmap} />
    </div>
  );
};
```

#### 2.3.3 Interactive Mermaid Diagrams

```typescript
const DiagramViewer: React.FC<{ diagram: string; title: string }> = ({ diagram, title }) => {
  return (
    <div className="diagram-viewer">
      <h3>{title}</h3>
      
      <div className="mermaid-container">
        <Mermaid chart={diagram} />
      </div>
      
      {/* Learning annotations */}
      <div className="diagram-explanation">
        <h4>Understanding this diagram:</h4>
        <ul>
          <li>↓ arrows show the flow of data/control</li>
          <li>Boxes represent different parts of the system</li>
          <li>Click any box to see the actual code</li>
        </ul>
      </div>
      
      <DiagramControls
        onZoom={handleZoom}
        onExport={exportAsPNG}
      />
    </div>
  );
};
```

---

## 3. Application Layer (Backend API)

### 3.1 Technology Stack
- **Framework**: FastAPI (Python) - fast, modern, async
- **Language**: Python 3.11+
- **Server**: Uvicorn (ASGI)
- **Deployment**: AWS Lambda + API Gateway (serverless)
- **Authentication**: GitHub OAuth (for private repos)

### 3.2 API Endpoints (Learning-Optimized)

```python
from fastapi import FastAPI, HTTPException
from pydantic import BaseModel, HttpUrl

app = FastAPI(title="OnboardAI Learning API")

# ========== Models ==========

class RepoAnalysisRequest(BaseModel):
    repo_url: HttpUrl
    github_token: str | None = None
    learning_level: str = "beginner"  # beginner, intermediate, advanced

class LearningQuery(BaseModel):
    repo_id: str
    question: str
    conversation_history: list[dict] = []  # For context-aware answers

class RoadmapRequest(BaseModel):
    repo_id: str
    time_available: int = 480  # minutes (8 hours default)
    focus_areas: list[str] = []  # e.g., ["authentication", "api"]

# ========== Learning-Focused Endpoints ==========

@app.post("/api/v1/analyze")
async def analyze_for_learning(request: RepoAnalysisRequest):
    """
    Analyze repository for learning
    
    Returns:
        - Tech stack with prerequisites
        - Learning complexity level
        - Entry points to start reading
        - Estimated learning time
    """
    try:
        # 1. Clone and filter repo
        repo_id = clone_repository(request.repo_url, request.github_token)
        
        # 2. Analyze code structure
        analysis = await analyze_codebase(repo_id)
        
        # 3. Detect learning prerequisites
        prereqs = detect_prerequisites(analysis)
        
        # 4. Generate learning roadmap
        roadmap = generate_learning_roadmap(analysis, request.learning_level)
        
        return {
            "repo_id": repo_id,
            "tech_stack": analysis['tech_stack'],
            "prerequisites": prereqs,  # ["JavaScript ES6", "React basics", "REST APIs"]
            "complexity": "intermediate",  # beginner/intermediate/advanced
            "estimated_learning_time": "6-8 hours",
            "entry_points": analysis['entry_points'],
            "roadmap_preview": roadmap[:3]  # First 3 learning topics
        }
    except Exception as e:
        raise HTTPException(status_code=500, detail=str(e))


@app.post("/api/v1/learn/ask")
async def ask_learning_question(query: LearningQuery):
    """
    Answer learning-focused questions with educational explanations
    
    Returns:
        - Educational explanation
        - Code references
        - Visual diagrams (if applicable)
        - Learning takeaways
        - Suggested next questions
    """
    # Load knowledge graph
    knowledge = load_knowledge_graph(query.repo_id)
    
    # Detect question intent
    intent = classify_question_intent(query.question)
    # Intents: factual, explanation, visual, guidance, pattern
    
    if intent == "visual":
        # Generate diagram
        diagram = await generate_learning_diagram(knowledge, query.question)
        return {
            "answer": "Here's a visual explanation:",
            "diagram": diagram,
            "diagram_type": "sequence",  # or flowchart, architecture
            "explanation": "This diagram shows..."
        }
    
    elif intent == "explanation":
        # Generate educational explanation
        context = retrieve_relevant_code(knowledge, query.question)
        explanation = await generate_educational_explanation(
            question=query.question,
            code_context=context,
            conversation_history=query.conversation_history
        )
        
        return {
            "answer": explanation['text'],
            "code_references": explanation['code_refs'],
            "learning_takeaways": explanation['key_concepts'],
            "suggested_next": explanation['related_questions']
        }
    
    else:  # factual or guidance
        answer = quick_lookup(knowledge, query.question)
        return {
            "answer": answer,
            "type": "quick_reference"
        }


@app.get("/api/v1/learn/roadmap/{repo_id}")
async def get_learning_roadmap(repo_id: str, time_available: int = 480):
    """
    Generate personalized learning roadmap
    
    Returns 3-phase learning path with:
        - Topics to learn in each phase
        - Files to read
        - Concepts to understand
        - Hands-on tasks
        - Time estimates
        - Learning checkpoints
    """
    knowledge = load_knowledge_graph(repo_id)
    
    roadmap = build_learning_roadmap(
        knowledge=knowledge,
        available_time_minutes=time_available
    )
    
    return {
        "total_phases": 3,
        "estimated_time": f"{time_available // 60} hours",
        "phases": roadmap,
        "learning_outcomes": [
            "Understand overall architecture",
            "Can explain main features",
            "Ready to make first contribution"
        ]
    }


@app.get("/api/v1/learn/patterns/{repo_id}")
async def get_learning_patterns(repo_id: str):
    """
    Detect and explain code patterns for learning
    
    Returns:
        - Design patterns found (MVC, Middleware, etc.)
        - Where they're implemented
        - Why they're used
        - Learning resources
    """
    patterns = detect_code_patterns(repo_id)
    
    return {
        "patterns_found": [
            {
                "name": "MVC Pattern",
                "explanation": "Separates data, logic, and presentation",
                "files": ["models/", "views/", "controllers/"],
                "learning_benefit": "Makes code easier to maintain and test"
            },
            # ... more patterns
        ],
        "learning_priority": "Start with MVC as it's the foundational pattern"
    }
```

### 3.3 Repository Ingestion (Learning-Optimized)

```python
class LearningOptimizedIngestion:
    """
    Smart repo filtering focused on learning value
    """
    
    LEARNING_RELEVANT_EXTENSIONS = [
        '.js', '.jsx', '.ts', '.tsx',  # JavaScript/TypeScript
        '.py',  # Python
        '.json',  # Config files
        '.md',  # Documentation
        '.env.example',  # Environment examples
    ]
    
    LEARNING_PRIORITY_FILES = [
        'README.md',  # Project overview
        'package.json', 'requirements.txt',  # Dependencies
        'docker-compose.yml',  # Architecture
        '.env.example',  # Configuration
    ]
    
    def filter_for_learning(self, repo_path: str) -> dict:
        """
        Keep only files that help learning
        Remove noise that wastes learner's time
        """
        learning_files = []
        
        for root, dirs, files in os.walk(repo_path):
            # Skip build/dependency folders
            dirs[:] = [d for d in dirs if d not in [
                'node_modules', '__pycache__', 'build', 'dist',
                '.git', 'coverage', '.next'
            ]]
            
            for file in files:
                ext = os.path.splitext(file)[1]
                
                if ext in self.LEARNING_RELEVANT_EXTENSIONS:
                    filepath = os.path.join(root, file)
                    
                    # Calculate learning value score
                    learning_value = self._calculate_learning_value(filepath)
                    
                    learning_files.append({
                        'path': filepath,
                        'type': 'source_code' if ext != '.md' else 'documentation',
                        'learning_value': learning_value,
                        'size': os.path.getsize(filepath)
                    })
        
        # Sort by learning value (entry points first, utilities last)
        learning_files.sort(key=lambda x: x['learning_value'], reverse=True)
        
        return {
            'total_files': len(learning_files),
            'high_priority': [f for f in learning_files if f['learning_value'] > 0.7],
            'medium_priority': [f for f in learning_files if 0.4 <= f['learning_value'] <= 0.7],
            'low_priority': [f for f in learning_files if f['learning_value'] < 0.4]
        }
    
    def _calculate_learning_value(self, filepath: str) -> float:
        """
        Score 0-1 indicating learning importance
        
        High value:
            - Entry points (index.js, main.py)
            - README, docs
            - Route definitions
            - Core models
        
        Low value:
            - Test files
            - Utilities
            - Type definitions
        """
        filename = os.path.basename(filepath)
        
        # Priority indicators
        if filename in self.LEARNING_PRIORITY_FILES:
            return 1.0
        
        if 'route' in filename.lower() or 'controller' in filename.lower():
            return 0.9  # Shows what app does
        
        if 'model' in filename.lower() or 'schema' in filename.lower():
            return 0.8  # Shows data structure
        
        if 'config' in filename.lower():
            return 0.7  # Shows configuration
        
        if 'util' in filename.lower() or 'helper' in filename.lower():
            return 0.3  # Less important for initial learning
        
        if 'test' in filename.lower() or 'spec' in filename.lower():
            return 0.2  # Can learn from tests but not priority
        
        return 0.5  # Default medium priority
```

---

## 4. Intelligence Layer (Teaching Engine)

### 4.1 Static Code Analyzer (Educational Focus)

```python
class EducationalCodeAnalyzer:
    """
    Analyzes code with learning objectives in mind
    """
    
    def analyze_for_learning(self, repo_path: str) -> dict:
        """
        Extract knowledge optimized for teaching
        
        Returns learning-relevant structure:
            - What the code does (high-level)
            - How it's organized
            - What patterns it uses
            - Where to start reading
        """
        analysis = {
            'entry_points': [],
            'core_components': [],
            'supporting_utilities': [],
            'patterns_detected': [],
            'learning_path': []
        }
        
        # Detect language
        language = self._detect_primary_language(repo_path)
        
        if language == 'python':
            analysis = self._analyze_python_for_learning(repo_path)
        elif language in ['javascript', 'typescript']:
            analysis = self._analyze_js_for_learning(repo_path)
        
        # Build learning-optimized dependency graph
        analysis['learning_path'] = self._build_learning_path(analysis)
        
        return analysis
    
    def _build_learning_path(self, analysis: dict) -> list:
        """
        Order files by learning dependency
        
        Foundation (no dependencies) → Core (few dependencies) → Advanced (many dependencies)
        """
        import networkx as nx
        
        # Build dependency graph
        G = nx.DiGraph()
        
        for file_info in analysis['files']:
            G.add_node(file_info['path'])
            for imported_file in file_info['imports']:
                G.add_edge(file_info['path'], imported_file)
        
        # Topological sort = learning order
        try:
            learning_order = list(nx.topological_sort(G))
        except nx.NetworkXError:
            # Cycle exists, use approximate order
            learning_order = list(G.nodes())
        
        return [
            {
                'order': idx + 1,
                'file': file,
                'reason': self._explain_learning_position(file, idx, len(learning_order))
            }
            for idx, file in enumerate(learning_order)
        ]
    
    def _explain_learning_position(self, file: str, position: int, total: int) -> str:
        """
        Explain why learner should read this file at this point
        """
        if position < total * 0.2:
            return "Foundation: Learn this first - has no dependencies"
        elif position < total * 0.6:
            return "Core: Builds on foundation concepts"
        else:
            return "Advanced: Integrates multiple concepts"
```

### 4.2 Knowledge Builder (Learning-Oriented)

```python
class LearningKnowledgeBuilder:
    """
    Builds knowledge graph optimized for teaching
    """
    
    def build_learning_graph(self, analysis: dict) -> dict:
        """
        Transform code analysis into teaching-friendly knowledge
        
        Nodes: Concepts (not just files)
        Edges: "teaches", "requires", "demonstrates"
        """
        graph = {
            'concepts': [],
            'relationships': [],
            'learning_paths': []
        }
        
        # Extract teachable concepts
        graph['concepts'] = self._extract_concepts(analysis)
        
        # Build prerequisite relationships
        graph['relationships'] = self._build_prerequisite_graph(graph['concepts'])
        
        # Generate learning paths
        graph['learning_paths'] = self._generate_learning_paths(graph)
        
        return graph
    
    def _extract_concepts(self, analysis: dict) -> list:
        """
        Identify concepts learner should understand
        
        Not just "this file exists" but "this concept is demonstrated"
        """
        concepts = []
        
        # Authentication concept
        if analysis['patterns']['authentication']:
            concepts.append({
                'name': 'User Authentication',
                'files': analysis['patterns']['authentication']['files'],
                'description': 'How users log in and stay logged in',
                'prerequisites': ['HTTP basics', 'Sessions/Tokens'],
                'learning_outcomes': [
                    'Understand JWT authentication',
                    'Know how middleware protects routes',
                    'Can add authentication to new route'
                ]
            })
        
        # Database concept
        if analysis['patterns']['database']:
            concepts.append({
                'name': 'Data Persistence',
                'files': analysis['patterns']['database']['models'],
                'description': 'How the app stores and retrieves data',
                'prerequisites': ['Basic SQL or NoSQL'],
                'learning_outcomes': [
                    'Understand data models',
                    'Know how to query database',
                    'Can add new model/table'
                ]
            })
        
        # ... more concepts
        
        return concepts
```

### 4.3 AI Reasoning Engine (Educational LLM Prompts)

```python
class EducationalLLMEngine:
    """
    Uses LLM to generate teaching-quality explanations
    """
    
    def __init__(self):
        import google.generativeai as genai
        genai.configure(api_key=os.getenv("GEMINI_API_KEY"))
        self.model = genai.GenerativeModel('gemini-1.5-flash')
    
    async def explain_for_learner(
        self,
        question: str,
        code_context: list[dict],
        learner_level: str = "beginner"
    ) -> dict:
        """
        Generate educational explanation
        
        Teaching principles:
            - Start with high-level concept
            - Break into simple steps
            - Define technical terms
            - Provide visual aids when possible
            - Suggest next learning steps
        """
        prompt = self._build_educational_prompt(
            question=question,
            code_context=code_context,
            level=learner_level
        )
        
        response = await self.model.generate_content_async(prompt)
        
        # Parse structured response
        parsed = self._parse_educational_response(response.text)
        
        return {
            'explanation': parsed['main_explanation'],
            'code_references': code_context,
            'learning_takeaways': parsed['key_concepts'],
            'suggested_next': parsed['next_questions'],
            'visual_aid': parsed.get('diagram_suggestion')
        }
    
    def _build_educational_prompt(
        self,
        question: str,
        code_context: list,
        level: str
    ) -> str:
        """
        Craft prompt optimized for teaching
        """
        prompt = f"""You are an expert programming teacher helping a {level} developer learn a codebase.

TEACHING GOALS:
1. Explain concepts clearly and concisely
2. Use beginner-friendly language
3. Define technical terms when first used
4. Break complex topics into simple steps
5. Provide concrete code examples
6. Suggest what to learn next

LEARNER'S QUESTION: "{question}"

RELEVANT CODE CONTEXT:
"""
        
        for ctx in code_context[:3]:
            prompt += f"\n[File: {ctx['file']}]\n{ctx['code_snippet']}\n"
        
        prompt += f"""

Please provide a teaching-quality explanation that:

1. CONCEPT OVERVIEW (2-3 sentences)
   - Start with the high-level idea
   - Explain WHY this code exists

2. STEP-BY-STEP EXPLANATION
   - Break it down into 3-5 clear steps
   - Reference specific code lines
   - Explain each technical term (e.g., "middleware: code that runs before route handlers")

3. LEARNING TAKEAWAYS
   - List 2-3 key concepts to remember
   - Format as: "📚 Learning: [concept]"

4. NEXT STEPS
   - Suggest 2-3 related questions to explore
   - Format as: "→ Learn next: [question]"

Keep your explanation to 200-300 words. Use clear, conversational language suitable for a {level} developer.
"""
        
        return prompt
    
    def _parse_educational_response(self, llm_text: str) -> dict:
        """
        Extract structured learning content from LLM response
        """
        # Parse sections
        sections = {
            'main_explanation': '',
            'key_concepts': [],
            'next_questions': []
        }
        
        # Extract learning takeaways (marked with 📚)
        import re
        takeaways = re.findall(r'📚 Learning: (.+)', llm_text)
        sections['key_concepts'] = takeaways
        
        # Extract next steps (marked with →)
        next_steps = re.findall(r'→ Learn next: (.+)', llm_text)
        sections['next_questions'] = next_steps
        
        # Main explanation is everything else
        sections['main_explanation'] = re.sub(
            r'(📚 Learning:.*|→ Learn next:.*)',
            '',
            llm_text
        ).strip()
        
        return sections
```

### 4.4 Learning Roadmap Generator

```python
class LearningRoadmapGenerator:
    """
    Generates personalized learning paths through codebase
    """
    
    def generate_roadmap(
        self,
        knowledge_graph: dict,
        time_available_minutes: int = 480  # 8 hours
    ) -> list:
        """
        Create 3-phase learning roadmap
        
        Phase 1 (25% time): Foundations
        Phase 2 (50% time): Core Features
        Phase 3 (25% time): Advanced Topics
        """
        total_concepts = len(knowledge_graph['concepts'])
        
        # Distribute time
        phase_times = {
            1: int(time_available_minutes * 0.25),
            2: int(time_available_minutes * 0.50),
            3: int(time_available_minutes * 0.25)
        }
        
        roadmap = [
            self._build_phase_1_foundations(knowledge_graph, phase_times[1]),
            self._build_phase_2_core(knowledge_graph, phase_times[2]),
            self._build_phase_3_advanced(knowledge_graph, phase_times[3])
        ]
        
        return roadmap
    
    def _build_phase_1_foundations(self, kg: dict, minutes: int) -> dict:
        """
        Phase 1: Get oriented in the codebase
        """
        return {
            'phase': 1,
            'title': 'Foundations: Understanding the Big Picture',
            'description': 'Get oriented with the tech stack and overall structure',
            'estimated_time': f"{minutes // 60} hours",
            'topics': [
                {
                    'title': '1. Tech Stack Overview',
                    'files': ['README.md', 'package.json'],
                    'concepts': ['What languages/frameworks are used', 'Project dependencies'],
                    'hands_on': ['Read README', 'Explore package.json dependencies']
                },
                {
                    'title': '2. Data Models',
                    'files': self._get_model_files(kg),
                    'concepts': ['What data this app manages', 'Database schema'],
                    'hands_on': ['Draw ER diagram of models']
                },
                {
                    'title': '3. Architecture Overview',
                    'files': [],
                    'concepts': ['How layers connect', 'Request flow basics'],
                    'hands_on': ['View architecture diagram', 'Trace one request end-to-end']
                }
            ],
            'checkpoint': 'Can explain overall architecture in 2-3 sentences'
        }
    
    def _build_phase_2_core(self, kg: dict, minutes: int) -> dict:
        """
        Phase 2: Learn main features
        """
        core_concepts = [c for c in kg['concepts'] if c.get('importance') == 'core']
        
        return {
            'phase': 2,
            'title': 'Core Features: How Things Work',
            'description': 'Deep dive into main functionality',
            'estimated_time': f"{minutes // 60} hours",
            'topics': [
                {
                    'title': f"4. {concept['name']}",
                    'files': concept['files'],
                    'concepts': concept['learning_outcomes'],
                    'hands_on': [f"Trace {concept['name']} flow", f"Try implementing similar feature"]
                }
                for concept in core_concepts[:3]
            ],
            'checkpoint': 'Can modify existing features with confidence'
        }
    
    def _build_phase_3_advanced(self, kg: dict, minutes: int) -> dict:
        """
        Phase 3: Advanced topics
        """
        return {
            'phase': 3,
            'title': 'Advanced Topics: Going Deeper',
            'description': 'Understand sophisticated patterns and optimizations',
            'estimated_time': f"{minutes // 60} hours",
            'topics': [
                {
                    'title': '7. Caching & Performance',
                    'files': self._get_cache_files(kg),
                    'concepts': ['When/why caching is used', 'Performance optimization'],
                    'hands_on': ['Measure performance impact', 'Add caching to new feature']
                },
                # ... more advanced topics
            ],
            'checkpoint': 'Ready to contribute independently'
        }
```

---

## 5. Data Layer (Learning-Optimized Storage)

### 5.1 Knowledge Graph Storage

```json
{
  "repo_id": "abc123",
  "metadata": {
    "name": "ecommerce-api",
    "tech_stack": ["Node.js", "Express", "PostgreSQL"],
    "complexity": "intermediate",
    "learning_time_estimate": "6-8 hours"
  },
  "learning_graph": {
    "concepts": [
      {
        "id": "auth",
        "name": "User Authentication",
        "files": ["auth/middleware.js", "routes/auth.js"],
        "prerequisites": ["HTTP basics", "JWT understanding"],
        "learning_outcomes": [
          "Understand token-based auth",
          "Can secure new routes"
        ]
      }
    ],
    "learning_paths": [
      {
        "path_id": "quickstart",
        "name": "Quick Start (4 hours)",
        "phases": [...]
      }
    ]
  }
}
```

---

## 6. Deployment Architecture (AWS Serverless)

```
┌──────────────────────────────────────────────────────────────┐
│                      AWS CLOUD                                │
│                                                                │
│  ┌────────────────────────────────────────────────────────┐  │
│  │  CloudFront CDN                                         │  │
│  │  • Next.js static frontend                             │  │
│  │  • Caching for fast learning experience               │  │
│  └──────────────┬─────────────────────────────────────────┘  │
│                 │                                              │
│  ┌──────────────▼─────────────────────────────────────────┐  │
│  │  API Gateway                                            │  │
│  │  • REST endpoints for learning queries                 │  │
│  │  • Rate limiting, CORS                                 │  │
│  └──────────────┬─────────────────────────────────────────┘  │
│                 │                                              │
│         ┌───────┼───────┬──────────┐                          │
│         ▼       ▼       ▼          ▼                          │
│  ┌──────────┐ ┌────────────┐ ┌────────────┐ ┌──────────┐    │
│  │ Analyze  │ │   Query    │ │  Roadmap   │ │ Cleanup  │    │
│  │ Lambda   │ │  Lambda    │ │   Lambda   │ │  Lambda  │    │
│  │ (60s)    │ │  (30s)     │ │   (10s)    │ │ (Cron)   │    │
│  └─────┬────┘ └──────┬─────┘ └──────┬─────┘ └──────────┘    │
│        │             │               │                         │
│  ┌─────▼─────────────▼───────────────▼──────────────────┐    │
│  │           S3 Storage                                  │    │
│  │  • Temporary repos (24hr TTL)                         │    │
│  │  • Knowledge graphs (JSON)                            │    │
│  │  • Learning roadmaps cache                            │    │
│  └───────────────────────────────────────────────────────┘    │
│                                                                │
│  ┌────────────────────────────────────────────────────────┐  │
│  │  CloudWatch Logs                                       │  │
│  │  • Learning query patterns                             │  │
│  │  • Performance metrics                                 │  │
│  └────────────────────────────────────────────────────────┘  │
└────────────────────────────────────────────────────────────────┘
```

### 6.1 Cost Estimate (Learning-Optimized MVP)

| Service | Usage | Monthly Cost |
|---------|-------|--------------|
| **Next.js Hosting** (Vercel) | Free tier | $0 |
| **Lambda** (3 functions) | 1000 invocations | $1 |
| **S3 Storage** | 50GB repos (auto-delete) | $1.15 |
| **API Gateway** | 1000 requests | $0.04 |
| **Gemini Flash API** | 1500/day free tier | $0 |
| **CloudWatch Logs** | Basic monitoring | $0.50 |
| **TOTAL** | | **~$3/month** 🎉 |

**Cost per learner**: $0.003 (with free tier Gemini)  
**Scalability**: Can serve 500 learners/month on free tier!

---

## 7. Learning Analytics (Future)

### 7.1 Learning Metrics to Track

```python
class LearningAnalytics:
    """
    Track learning effectiveness (privacy-respecting)
    """
    
    def track_learning_session(self, session_data: dict):
        """
        Aggregate anonymous learning metrics
        """
        metrics = {
            # Effectiveness
            'avg_time_to_first_contribution': timedelta,
            'concept_mastery_rate': float,  # % who pass checkpoints
            'question_resolution_rate': float,  # % questions answered
            
            # Engagement
            'avg_session_duration': timedelta,
            'questions_per_session': int,
            'diagrams_generated': int,
            'roadmap_completion_rate': float,
            
            # Learning paths
            'most_common_first_questions': list[str],
            'average_learning_path': list[str],
            'dropout_points': list[str]  # Where learners get stuck
        }
```

---

## 8. Technology Choices Rationale

| Component | Technology | Why (Learning Focus) |
|-----------|------------|----------------------|
| **Frontend** | Next.js + TypeScript | SEO for learning content, type safety reduces bugs |
| **UI** | Tailwind CSS | Rapid prototyping, consistent learning UI |
| **Diagrams** | Mermaid.js | Renders beautiful diagrams from text, no image processing |
| **Backend** | FastAPI | Fast async API, auto-generated docs |
| **Code Analysis** | Python AST | Official parser, 100% accurate |
| **AI** | Gemini Flash | Free tier (1500/day), fast responses, teaching-quality |
| **Deployment** | AWS Lambda | Pay-per-use, scales to zero, cost-effective for learning |
| **Storage** | S3 + JSON | Simple, no database needed for MVP |

---

## 9. Revision History

| Version | Date | Author | Changes |
|---------|------|--------|---------|
| 1.0 | 2026-02-13 | Team: The Third Lab | Design for Learning & Productivity track |

---

**Document Status**: Ready for Implementation  
**Track**: AI for Learning & Developer Productivity ✅  
**Focus**: Teaching-optimized architecture for helping developers learn faster 🚀
