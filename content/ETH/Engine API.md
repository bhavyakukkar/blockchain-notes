
- Engine API plays a central role in Ethereum's post-merge architecture, defining a standardised RPC interface between the Consensus Layer (CL) and Execution Layer (EL). The CL is responsible for agreeing on the canonical chain and finalising blocks, while the EL handles block creation, processing and execution, state management, blockchain storage, mempool management, RPC interfaces, and more.

  From the perspective of Engine API, the CL is a client that makes RPC calls with Engine API methods
to the EL, the RPC server. Key methods are:

  - `forkchoiceUpdated`: Updates the execution client with the latest chain head and final block. If
    called with a `PayloadAttributes` parameter, it instructs the client to build a new block. This
    method also plays a role in Ethereum's finality mechanism by marking blocks as finalised.
  - `getPayload`: Retrieves a newly constructed block from the execution client after calling
    `forkchoiceUpdated` with `PayloadAttributes`.
  - `newPayload`: Submits a proposed block to the execution client for validation and inclusion in the
    chain. Note that it does not change the tip of the chain, which is the job of `forkchoiceUpdated`.

  <https://github.com/informalsystems/malaketh-layered?tab=readme-ov-file#engine-api>
