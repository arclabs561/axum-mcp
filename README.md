# axum-mcp

HTTP transport for Model Context Protocol servers.

## Install

```toml
[dependencies]
axum-mcp = "0.2"
```

## Example

```rust
use axum_mcp::{extract_string, McpServer, Tool};
use async_trait::async_trait;
use serde_json::Value;

struct EchoTool;

#[async_trait]
impl Tool for EchoTool {
    fn description(&self) -> &str { "Echo input" }

    fn schema(&self) -> Value {
        serde_json::json!({
            "type": "object",
            "properties": {"text": {"type": "string"}},
            "required": ["text"]
        })
    }

    async fn call(&self, args: &Value) -> Result<Value, String> {
        let text = extract_string(args, "text")?;
        Ok(serde_json::json!({ "echoed": text }))
    }
}

#[tokio::main]
async fn main() -> Result<(), Box<dyn std::error::Error>> {
    let server = McpServer::new().tool("echo", EchoTool)?;
    server.serve("127.0.0.1:8080").await?;
    Ok(())
}
```

## Endpoints

- `GET /health`
- `GET /tools/list`
- `POST /tools/call`
- `GET /resources/list`
- `POST /resources/read`
- `GET /prompts/list`
- `POST /prompts/get`

## License

See `LICENSE`.
