# AI-Enhanced Climate Risk News Dashboard: Documentation

**Demo Link**: [Watch the Demo Video](https://www.loom.com/share/08999e177e504d30bd1839bfe3c436c4?sid=93cf88bb-d1a7-462b-af42-e1030036083f)

**Demo Link**: [Watch the Demo Video](https://www.loom.com/share/08999e177e504d30bd1839bfe3c436c4?sid=93cf88bb-d1a7-462b-af42-e1030036083f)


## Overview

The AI-Enhanced Climate Risk News Dashboard is a specialized tool designed to help insurance professionals monitor, analyze, and assess climate-related news for potential business impacts. The system combines web scraping, natural language processing, and AI-driven risk assessment to provide actionable insights on how climate events might affect insurance technologies and risk assessment frameworks.

## Technology Stack

### Core Technologies
- **Python**: Primary development language
- **Gradio**: Web interface framework for creating the interactive dashboard
- **Langchain**: Framework for building LLM applications and connecting to various tools
- **Google Gemini Pro**: AI model used for text summarization and risk assessment
- **Tavily Search API**: Used for fetching relevant news articles
- **Matplotlib & WordCloud**: Data visualization libraries

### Key Dependencies
- `gradio`: Frontend UI framework
- `langchain_community.tools.tavily_search`: News retrieval
- `langchain_google_genai`: Gemini AI integration
- `matplotlib`: Visualization
- `wordcloud`: Topic visualization
- `dotenv`: Environment variable management

## System Architecture

The dashboard follows a modular architecture with distinct components:

1. **News Retrieval Module**: Fetches news articles using the Tavily Search API
2. **AI Analysis Module**: Processes articles with Google Gemini Pro for:
   - Concise summarization
   - Insurance impact assessment
   - Risk level classification (1-5 scale)
3. **Visualization Module**: Creates word clouds of key topics
4. **User Interface**: Interactive Gradio interface with search and pagination

## Implementation Details

### News Retrieval Process
The system uses Tavily's search capabilities to fetch up to 20 news articles per query, with advanced search depth to ensure comprehensive coverage. Results are deduplicated by URL to avoid redundancy.

```python
news_tool = TavilySearchResults(
    max_results=20,
    search_depth="advanced",
    include_answer=True,
    include_raw_content=True,
)
```

### AI Analysis Pipeline
Each news article undergoes a three-stage AI analysis:

1. **Summarization**: Condenses article content to 2-3 sentences
   ```python
   summary_prompt = PromptTemplate(
       input_variables=["text"],
       template="Summarize this article in 2-3 sentences: {text}"
   )
   ```

2. **Impact Assessment**: Evaluates potential effects on insurance technology
   ```python
   impact_prompt = PromptTemplate(
       input_variables=["text"],
       template="Based on this article, explain in 1-2 sentences how it might affect insurance technology or risk assessment: {text}"
   )
   ```

3. **Risk Classification**: Assigns a numeric risk level (1-5) with explanation
   ```python
   risk_assessment_prompt = PromptTemplate(
       input_variables=["text"],
       template="""Based on the content of this news article, assess the risk level for insurance companies on a scale of 1 to 5 (where 1 is very low risk and 5 is very high risk).
       Also provide a brief explanation of WHY this risk level was assigned based on the article's content.
       Format your response as:
       RISK_LEVEL: [number 1-5]
       EXPLANATION: [brief explanation of why this risk level was assigned]
       """
   )
   ```

### Fallback Mechanism
The system includes a rule-based fallback for scenarios where AI processing fails:

```python
def rule_based_summary_and_impact(text):
    # Simple extractive summarization - get first 2 sentences
    sentences = re.split(r'(?<=[.!?])\s+', text)
    summary = ' '.join(sentences[:2]) if len(sentences) > 2 else text[:200] + "..."

    text_lower = text.lower()
    if "insurance" in text_lower and "technology" in text_lower:
        impact = "This article discusses impacts on insurance technology directly."
    elif "insurance" in text_lower:
        impact = "This article may have implications for insurance processes and policies."
    elif "risk" in text_lower and ("assessment" in text_lower or "management" in text_lower):
        impact = "This content relates to risk assessment methodologies which are foundational to insurance."
    else:
        impact = "Limited direct insurance technology impact based on keyword analysis."
    return summary, impact
```

### Risk Visualization
Risk levels are color-coded for quick visual assessment:
- **1 (Very Low Risk)**: Green (#2ecc71)
- **2 (Low Risk)**: Blue (#3498db)
- **3 (Moderate Risk)**: Yellow (#f1c40f)
- **4 (High Risk)**: Orange (#e67e22)
- **5 (Very High Risk)**: Red (#e74c3c)

### Word Cloud Generation
The dashboard creates word clouds to visualize prevalent topics across all retrieved articles:

```python
def generate_wordcloud(articles):
    # Extract all text from articles
    all_text = " ".join([article.get('content', '') for article in articles])

    # Clean text - remove URLs, special chars, and common words
    all_text = re.sub(r'http\S+', '', all_text.lower())
    all_text = re.sub(r'[^\w\s]', '', all_text)

    # Remove common words
    stopwords = {'the', 'and', 'of', 'to', 'a', 'in', 'for', 'is', 'on', 'that', 'by', 'this', 'with', 'i', 'you', 'it', 'not', 'or', 'be', 'are', 'from', 'at', 'as', 'your', 'have', 'more', 'has', 'an', 'was', 'we', 'will', 'can', 'they', 'their', 'said', 'but', 'what'}
    word_list = [word for word in all_text.split() if word not in stopwords and len(word) > 3]

    # Count word frequencies
    word_counts = Counter(word_list)

    # Generate word cloud
    wordcloud = WordCloud(width=800, height=400, background_color='white', max_words=100).generate_from_frequencies(word_counts)
    
    # [Image rendering code...]
```

## User Interface

The Gradio-based interface provides:
- Search functionality with pre-populated example queries
- Pagination controls for navigating through results
- Word cloud visualization of key topics
- Detailed article cards with:
  - Title and source
  - AI-generated summary
  - Insurance technology impact assessment
  - Risk level with color-coding and explanation
- Risk level legend for easy interpretation

## Usage Guide

### Basic Usage
1. Enter a search term related to climate risk and insurance in the search box
2. Click "Search" to retrieve and analyze relevant news
3. Review the word cloud to identify key topics
4. Browse through analyzed articles with their summaries and risk assessments
5. Use pagination controls to navigate through multiple pages of results

### Example Queries
- "Climate Risk Insurance"
- "Parametric Insurance for Natural Disasters"
- "Climate Change Risk Assessment Models"
- "Insurance Technology Innovations for Climate Risk"

### Interpreting Results
- **Word Cloud**: Larger words indicate more frequent mentions across articles
- **Risk Level**: 
  - 1 (Green): Very low risk to insurance companies
  - 2 (Blue): Low risk
  - 3 (Yellow): Moderate risk
  - 4 (Orange): High risk
  - 5 (Red): Very high risk
- **Impact Analysis**: Concise explanation of potential effects on insurance technologies or risk assessment frameworks

## Performance Metrics and KPIs

### Technical Performance KPIs
1. **Response Time**:
   - Search result retrieval time (target: <3 seconds)
   - AI analysis processing time (target: <5 seconds per article)

2. **System Reliability**:
   - API success rate (target: >98%)
   - Fallback mechanism activation frequency (<5% of requests)

3. **Content Coverage**:
   - Number of unique news sources represented
   - Article freshness (% of articles published within last 7 days)

### Analysis Quality KPIs
1. **Summary Quality**:
   - Information preservation score (manual evaluation)
   - Conciseness (target: 2-3 sentences)

2. **Risk Assessment Accuracy**:
   - Expert agreement score (manual validation by insurance risk professionals)
   - Risk level distribution analysis (checking for bias toward specific levels)

3. **Impact Analysis Relevance**:
   - Insurance technology relevance score (manual evaluation)
   - Actionability of insights (qualitative assessment)

### User Experience KPIs
1. **Engagement**:
   - Average session duration
   - Number of searches per session
   - Pagination usage rate

2. **Feature Utilization**:
   - Search refinement rate
   - Example query selection rate

3. **Satisfaction**:
   - User feedback score (if implemented)
   - Return usage rate

## Best Practices and Limitations

### Best Practices
1. Use specific search terms related to climate risk and insurance
2. Review multiple articles to identify patterns in risk assessments
3. Consider both the risk level and explanation when making decisions
4. Use the word cloud to identify emerging topics for follow-up searches

### Known Limitations
1. **AI Analysis Constraints**:
   - Risk assessments are based on article content, not comprehensive market analysis
   - Gemini Pro has a knowledge cutoff date and may not reflect the most recent developments
   - Long articles are truncated to 4000 characters for analysis

2. **Search Limitations**:
   - Limited to 20 results per query
   - Depends on Tavily API availability and content coverage

3. **Technical Considerations**:
   - Requires valid API keys for Tavily and Google Gemini
   - Default fallback to moderate risk level (3) when analysis fails

## Future Development Roadmap

### Short-term Improvements
- Add sentiment analysis for each article
- Implement trend detection across multiple searches
- Add export functionality for reports

### Medium-term Enhancements
- Integrate historical data comparison
- Add customizable risk threshold alerts
- Implement user accounts for saved searches

### Long-term Vision
- Create predictive models based on news patterns
- Integrate with internal insurance risk systems
- Add automated action recommendations based on risk assessments

## Conclusion

The AI-Enhanced Climate Risk News Dashboard provides insurance professionals with an efficient way to monitor and assess climate-related news for potential business impacts. By combining advanced search capabilities with AI-driven analysis, the tool enables quick identification of emerging risks and opportunities in the climate insurance landscape.
