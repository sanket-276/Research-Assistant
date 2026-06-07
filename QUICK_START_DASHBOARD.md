# 🚀 Quick Start Guide - Advanced Dashboard

## ⚡ Getting Started (2 Minutes)

### 1. Start the Servers

Open **two terminals**:

**Terminal 1 - Backend:**
```bash
cd research_assistant
python manage.py runserver
```

**Terminal 2 - Frontend:**
```bash
cd research-assistant-frontend
npm run dev
```

### 2. Open the Application

Click the **preview button** in your IDE or visit:
- **http://localhost:5175** (or the port shown in Terminal 2)

### 3. Login

Use your existing account credentials:
- Username: `your_username`
- Password: `your_password`

---

## 🎯 Quick Feature Tour

### Upload Documents (3 Ways)

1. **Drag & Drop**: Drag a PDF file onto the upload zone
2. **Click Upload**: Click "+ Upload Document" button
3. **Command Palette**: Press `Ctrl+K` → Select "Upload Document"

### View Your Documents

- **Grid View**: Click ⊞ icon (default)
- **List View**: Click ☰ icon for compact view

### Search Documents

1. Type your query in the search box
2. Press `Enter` to search
3. Results appear with relevance ranking

### Filter & Sort

- **Filter by Status**: Choose All/Completed/Processing/Failed
- **Sort**: Select Recent/Name/Size from dropdown

### Batch Operations

1. **Select documents**: Check boxes on multiple documents
2. **Delete multiple**: Click "Delete Selected" button
3. **Clear selection**: Click "Clear Selection"

### Mark Favorites

- Click the ☆ icon on any document
- It turns to ⭐ when favorited
- Quick access to important documents

### View Analytics

**Stats Cards** show:
- Total documents count
- Storage used
- Chat sessions
- Recent activities

**Upload Trend Chart** shows:
- Last 7 days of uploads
- Visual bar chart
- Hover to see counts

### Activity Feed

- **Recent 10 actions** displayed in sidebar
- Icons for each action type:
  - 📤 Upload
  - 🗑️ Delete
  - 💬 Chat
  - 📝 Summary
  - 🔍 Search

### Quick Actions

**Sidebar shortcuts:**
- 💬 New Chat - Start a conversation
- 🔍 Search All - Quick search

### Keyboard Shortcuts

- `Ctrl+K` (⌘K on Mac) - Open command palette
- `Escape` - Close command palette
- `Enter` - Execute search

---

## 📝 Common Tasks

### Generate Document Summary

1. Find your document in the list
2. Click **"Generate Summary"** button
3. Wait for processing (status updates automatically)
4. Summary appears below document name

### Start Chat with Document

1. Find your document
2. Click **"Start Chat"** button
3. Redirects to chat page
4. Begin asking questions!

### Delete a Document

**Single Delete:**
1. Find document
2. Click **"Delete"** button
3. Confirm deletion

**Batch Delete:**
1. Select multiple documents (checkboxes)
2. Click **"Delete Selected"**
3. Confirm deletion

### Toggle Favorite

1. Click ☆ icon on any document
2. It changes to ⭐ (favorited)
3. Click again to unfavorite

---

## 🎨 UI Elements Guide

### Document Card Information

Each document card shows:
- ✅ **Filename** - Name of the document
- ✅ **File Size** - Formatted size (MB, KB, etc.)
- ✅ **Page Count** - Number of pages
- ✅ **Upload Date** - Relative time (5m ago, 2h ago)
- ✅ **Status Badge** - Processing status
- ✅ **Summary** - If generated
- ✅ **Actions** - Generate Summary, Start Chat, Delete

### Status Badge Colors

- 🟢 **Green** - Completed (ready to use)
- 🔵 **Blue** - Processing (please wait)
- 🔴 **Red** - Failed (error occurred)
- ⚪ **Gray** - Pending (queued)

### Stats Card Icons

- 📄 **Documents** - Blue card
- 💾 **Storage** - Purple card
- 💬 **Chats** - Green card
- ⚡ **Activity** - Orange card

---

## 🐛 Troubleshooting

### Dashboard Not Loading?

1. Check both servers are running
2. Refresh the page (F5)
3. Clear browser cache
4. Check browser console for errors

### Upload Not Working?

1. Ensure file is PDF format
2. Check file size (not too large)
3. Wait for processing to complete
4. Check backend terminal for errors

### Stats Not Showing?

1. Upload at least one document first
2. Wait a few seconds for data to load
3. Refresh the page
4. Check network tab in browser dev tools

### Search Not Working?

1. Ensure documents are uploaded
2. Wait for indexing to complete (status = completed)
3. Try simpler search terms
4. Check that documents contain searchable text

---

## 💡 Pro Tips

### Efficiency Tips

1. **Use Keyboard Shortcuts**: `Ctrl+K` for quick access
2. **Batch Operations**: Select multiple docs for faster deletion
3. **Favorites**: Star important documents for quick access
4. **Grid View**: Better for browsing with images
5. **List View**: Better for scanning many documents

### Organization Tips

1. **Use Descriptive Filenames**: Easier to search and find
2. **Generate Summaries**: Helps identify document content quickly
3. **Check Activity Feed**: Track your recent actions
4. **Monitor Upload Trend**: See your usage patterns

### Search Tips

1. **Be Specific**: Use precise terms for better results
2. **Use Filters**: Combine search with status filters
3. **Sort Results**: Try different sort orders
4. **Check Summary**: Read summaries before opening docs

---

## 📞 Need Help?

Check these resources:

1. **Full Documentation**: See [`DASHBOARD_UPGRADE_SUMMARY.md`](./DASHBOARD_UPGRADE_SUMMARY.md)
2. **API Documentation**: Check endpoint details in summary
3. **Backend Logs**: Check Terminal 1 for server errors
4. **Frontend Console**: Open browser DevTools → Console

---

## 🎉 You're All Set!

Enjoy your **advanced Research Assistant dashboard** with:

✅ Modern, beautiful UI
✅ Powerful analytics
✅ Efficient workflows
✅ Real-time updates
✅ Keyboard shortcuts

**Happy researching! 📚✨**
