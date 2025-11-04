# Labøvelse 2: Legg til nytt verktøy

## La oss lage et "Tilfeldig Fakta" verktøy i MCP serveren

### Steg 1: Legg til faktum-endpoint i MCP Server

```python
# I services/mcp-server/app.py

class FactRequest(BaseModel):
    category: str = "general"

@app.post("/fact")
async def get_random_fact(request: FactRequest):
    """Få et tilfeldig interessant faktum."""
    facts = {
        "general": ["Honningbien produserer mat spist av mennesker.",
                   "Bananer er bær, men jordbær er ikke det."],
        "space": ["En dag på Venus er lengre enn året sitt.",
                 "Saturn ville flyte i vann."]
    }
    
    import random
    fact = random.choice(facts.get(request.category, facts["general"]))
    result = {"category": request.category, "fact": fact}
    
    # MCP-compliant response format
    return {
        "content": [{"type": "text", "text": json.dumps(result)}],
        "structuredContent": result,
        "isError": False
    }
```

---

# Labøvelse 2: Oppdater tools manifest

### Steg 2: Legg til i /tools endpoint

```python
# I services/mcp-server/app.py, oppdater list_tools():

@app.get("/tools")
async def list_tools():
    tools = [
        {
            "name": "get_weather_forecast",
            "description": "Hent værprognose for en destinasjon",
            "inputSchema": { /* ... */ }
        },
        {
            "name": "get_random_fact",
            "title": "Random Fact Provider",
            "description": "Få et tilfeldig interessant faktum",
            "inputSchema": {
                "type": "object",
                "properties": {
                    "category": {
                        "type": "string",
                        "description": "Faktakategori (general, space)",
                        "default": "general"
                    }
                }
            },
            "outputSchema": {
                "type": "object",
                "properties": {
                    "category": {"type": "string"},
                    "fact": {"type": "string"}
                }
            },
            "endpoint": "/fact",
            "method": "POST"
        }
    ]
    return {"tools": tools}
```