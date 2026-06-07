# Ranked Sources Feature

## Overview
When users select multiple PDFs and ask questions in chat, the system now displays all matching sources ranked by relevance/accuracy, with the most accurate answer at the top.

## Changes Made

### Backend (services.py)
1. **Increased Source Limit**: Changed from top 3 to top 5 sources
2. **Added Score & Rank**: Each source now includes:
   - `score`: Relevance/accuracy score (0-1)
   - `rank`: Position ranking (1 = most relevant)

### Frontend (EnhancedChatView.jsx)
1. **Ranked Display**: Sources are sorted and displayed by rank
2. **Visual Rank Indicators**:
   - 🥇 **Rank 1**: "Most Relevant" - Yellow badge, highest accuracy
   - 🥈 **Rank 2**: "High Relevance" - Orange badge
   - 🥉 **Rank 3**: "Good Match" - Amber badge
   - 📌 **Rank 4+**: "Relevant" - Blue badge

3. **Enhanced Source Cards** showing:
   - Rank emoji and badge (🥇, 🥈, 🥉)
   - Relevance label ("Most Relevant", "High Relevance", etc.)
   - Document filename
   - Page number
   - Match percentage (e.g., "85.3% match")
   - Large rank number watermark
   - Full quote with proper formatting
   - Link to view full document

## User Experience

### Example Scenario:
User selects 3 PDFs: "ML_Basics.pdf", "AI_Advanced.pdf", "Statistics.pdf"

User asks: **"What is machine learning?"**

**Response Shows**:
1. 🥇 **Most Relevant** - ML_Basics.pdf, Page 5, 94.2% match
   - Direct definition of machine learning
   
2. 🥈 **High Relevance** - AI_Advanced.pdf, Page 12, 87.5% match
   - Explains ML in context of AI
   
3. 🥉 **Good Match** - Statistics.pdf, Page 23, 76.8% match
   - Discusses statistical foundations of ML
   
4. 📌 **Relevant** - ML_Basics.pdf, Page 45, 68.3% match
   - Example applications
   
5. 📌 **Relevant** - AI_Advanced.pdf, Page 78, 65.1% match
   - Historical context

## Technical Details

### Scoring Algorithm
Sources are ranked using a combined score from:
- **Semantic Search (50%)**: ChromaDB embeddings similarity
- **TF-IDF (30%)**: Term frequency relevance
- **Keyword Matching (20%)**: Direct keyword matches

### Visual Hierarchy
- **Border colors**: Yellow > Orange > Amber > Blue
- **Background tint**: Matching the border color
- **Rank watermark**: Large semi-transparent rank number
- **Percentage display**: Shows exact match score

## Benefits
1. **Quick Identification**: Users instantly see the most accurate source
2. **Multiple Perspectives**: Access to all relevant sources ranked by quality
3. **Transparent Scoring**: See exact match percentages
4. **Better Research**: Compare information across multiple documents
5. **Visual Clarity**: Color-coded badges for quick scanning
