# Troubleshooting "Could not find any relevant information"

## Error Message
When you ask a question in chat, you see: **"Could not find relevant information in the selected documents. Try rephrasing your question or check if the documents contain information about this topic."**

## Common Causes & Solutions

### 1. Documents Not Properly Indexed

**Symptom**: Documents were uploaded but search returns no results.

**Solution**:
1. Check document processing status in Dashboard
2. Look for documents with "processing" or "failed" status
3. If status is "failed", delete and re-upload the document
4. Check backend logs for indexing errors

**To verify indexing**:
```bash
# Check ChromaDB collection
python manage.py shell
>>> from research_assistant.api.services import COLLECTION
>>> print(COLLECTION.count())  # Should be > 0
>>> print(COLLECTION.peek())   # Should show document chunks
```

### 2. Query Too Specific or Mismatched

**Symptom**: Question uses different terminology than document content.

**Examples**:
- ❌ Asking "What is ML?" when document says "Machine Learning"
- ❌ Asking "How does AI work?" when document only discusses specific algorithms
- ❌ Using technical jargon when document uses plain language

**Solutions**:
- Rephrase question using different terms
- Try more general questions first
- Use keywords from the document titles
- Ask simpler questions

**Good Question Formats**:
- ✅ "What is machine learning?"
- ✅ "Explain the main concepts in this document"
- ✅ "What are the key findings?"
- ✅ "Summarize the main points"

### 3. Empty or Image-Only PDFs

**Symptom**: PDF uploaded successfully but contains no searchable text.

**Common Cases**:
- Scanned documents (images, not text)
- PDFs with only images/charts
- Password-protected PDFs
- Corrupted PDF files

**Solution**:
- Ensure PDFs contain actual text (you can copy-paste text from PDF viewer)
- For scanned documents, use OCR software first
- Convert image-based PDFs to text-based PDFs

### 4. ChromaDB Collection Not Initialized

**Symptom**: First time using the app or after database reset.

**Solution**:
```bash
# Restart the Django server
python manage.py runserver

# This will auto-initialize ChromaDB on startup
```

### 5. Document IDs Mismatch

**Symptom**: Chat started with documents, but they're not being searched.

**Debug Steps**:
1. Check browser console (F12) for errors
2. Look at network tab when sending chat message
3. Verify document IDs are being sent in request

**Solution**: Start a new chat session with the documents

## Quick Diagnostic Checklist

- [ ] Document shows "completed" status (not "processing" or "failed")
- [ ] PDF contains actual text (not just images)
- [ ] Question is relevant to document content
- [ ] Documents are selected before starting chat
- [ ] Backend server is running without errors
- [ ] Browser console shows no JavaScript errors

## Manual Testing Steps

### Step 1: Verify Document Indexed
```python
# In Django shell
from research_assistant.api.models import Document
from research_assistant.api.services import COLLECTION

# Get your document
doc = Document.objects.first()
print(f"Document ID: {doc.id}, Filename: {doc.filename}")

# Check if it's in ChromaDB
results = COLLECTION.get(where={"document_id": str(doc.id)})
print(f"Chunks indexed: {len(results['documents'])}")

# Sample some content
if results['documents']:
    print(f"Sample text: {results['documents'][0][:200]}")
```

### Step 2: Test Retrieval Directly
```python
# In Django shell
from research_assistant.api.search_helpers import combined_retrieval

# Test search
doc_ids = [1]  # Replace with your document ID
query = "machine learning"  # Replace with your query
results = combined_retrieval(doc_ids, query, top_k=5)

print(f"Found {len(results)} results")
for r in results:
    print(f"Score: {r['score']:.2f}, Text: {r['text'][:100]}")
```

### Step 3: Check TF-IDF Index
```python
# In Django shell
from research_assistant.api.models import TFIDFIndex

# Check if TF-IDF entries exist
count = TFIDFIndex.objects.count()
print(f"TF-IDF entries: {count}")

# Check for specific document
doc_id = 1  # Replace with your document ID
doc_entries = TFIDFIndex.objects.filter(document_id=doc_id).count()
print(f"TF-IDF entries for doc {doc_id}: {doc_entries}")
```

## Recent Changes That May Help

### Improved Search (Latest Update)
- Increased top_k from 3 to 5 sources
- Retrieve more candidates (top_k * 2) before filtering
- Better error messages with suggestions
- Added logging for debugging

### Better Error Message
Old: "Could not find any relevant information."
New: "Could not find relevant information in the selected documents. Try rephrasing your question or check if the documents contain information about this topic."

## If Issue Persists

1. **Check Backend Logs**: Look for Python errors during search
2. **Restart Services**: Restart Django server
3. **Re-index Documents**: Delete and re-upload problematic PDFs
4. **Verify File Integrity**: Ensure PDF is not corrupted
5. **Check Disk Space**: Ensure ChromaDB has space to store indexes

## Need More Help?

If you've tried all the above and still getting no results, collect this information:
- Document filename and size
- Exact question being asked
- Backend log output
- Screenshot of error
- Output from diagnostic scripts above
