# Simple speculative execution

The easiest speculative execution policy available.  It starts another
execution of a query after constant delay of `retry_interval` and does at most
`max_retry_count` speculative query executions (not counting the first,
non-speculative one).

### Example
To use this policy in `Session`:
```rust
# extern crate scylla;
# use scylla::client::session::Session;
# use std::error::Error;
# async fn check_only_compiles() -> Result<(), Box<dyn Error>> {
use std::{sync::Arc, time::Duration};
use scylla::client::session::Session;
use scylla::client::session_builder::SessionBuilder;
use scylla::{
    policies::speculative_execution::SimpleSpeculativeExecutionPolicy,
    client::execution_profile::ExecutionProfile,
};

let policy = SimpleSpeculativeExecutionPolicy {
    max_retry_count: 3,
    retry_interval: Duration::from_millis(100),
};

let handle = ExecutionProfile::builder()
    .speculative_execution_policy(Some(Arc::new(policy)))
    .build()
    .into_handle();

let session: Session = SessionBuilder::new()
    .known_node("127.0.0.1:9042")
    .default_execution_profile_handle(handle)
    .build()
    .await?;
# Ok(())
# }
```
