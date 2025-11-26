<h1 align="center"><u style="font-size: 60px;">🔵AIDD 30-Day Challenge — Task 6  by (Bilqees Shahid) 🔵</u></h1>

# 🚀 Connecting GitHub with Gemini CLI using MCP

## GitHub Personal Access Token (Classic) 

# ⚙️ .env

![Github-token](./images/task63.jpg) 


# ⚙️ Settings.json

# ✏️ Add MCP in settings.json

```python
"mcpServers": {
    "github": {
      "httpUrl": "https://api.githubcopilot.com/mcp/",
      "headers": {
        "Authorization": "Bearer $GITHUB_MCP_PAT"      },
      "timeout": 5001
    }
  }
```
![Settings.json](./images/task62.jpg) 


# 🎯GitHub MCP Server Configured Successfully!
 
![Github-MCP](./images/task61.jpg)

## 📜 MCP List using command  /mcp list

- List of tools on Github MCP
---

![Github-MCP](./images/mcplist.jpg)

# 📚 GitHub repos list output

Prompt

```python
List my GitHub repositories
```
![Github-id](./images/task64.jpg)

![Github-repos](./images/task66.jpg)

![Github-repos](./images/repo.jpg)






