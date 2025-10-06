# sol_icp_poc

This project demonstrates an Internet Computer canister that bridges ICP and Solana flows. The backend canister is written in Rust and the frontend ships as static ES modules so it can run inside environments without a bundler—such as the [ICP Ninja](https://ninja.icp) editor.

## Frontend structure

- `src/sol_icp_poc_frontend/assets/index.html` bootstraps the UI and loads `main.js` as a module.
- `src/sol_icp_poc_frontend/assets/main.js` talks directly to the backend canister using DFINITY libraries served from a CDN.
- `src/sol_icp_poc_frontend/assets/sol_icp_poc_backend.idl.js` exports the Candid interface that `main.js` consumes.
- `src/sol_icp_poc_frontend/assets/canister_ids.json` provides network-specific canister IDs for local testing.

Because every dependency is loaded from a CDN, the frontend no longer needs `npm install`, Webpack, or Vite. Editing the assets in ICP Ninja works out of the box.

## Running inside ICP Ninja

1. Upload the repository to ICP Ninja.
2. Ensure the backend canister ID is available:
   - If you have already deployed the backend to mainnet, update `src/sol_icp_poc_frontend/assets/canister_ids.json` to include the `ic` canister ID.
   - To point at a locally deployed backend, add a `local` entry with the replica canister ID and start the replica before opening the editor.
3. Open `src/sol_icp_poc_frontend/assets/index.html` in the in-browser preview. The module loader will fetch DFINITY libraries from `cdn.jsdelivr.net` automatically.

## Local development workflow

```bash
# Start a local replica
dfx start --background

# Deploy backend and asset canisters, regenerating the candid bindings
dfx deploy
```

After deployment the asset canister URL (shown in the deploy output) will serve `index.html`. Append `?canisterId=<asset_canister_id>` when opening it locally. Update `src/sol_icp_poc_frontend/assets/canister_ids.json` with the generated backend ID so the frontend can discover the right canister.

To stop the local replica run `dfx stop`.

## Configuration files

- `canister_ids.json` (project root) mirrors the format that `dfx deploy --network ic` produces. Keep it in sync with any live deployments so the CDN-loaded frontend knows which backend to talk to.
- `dfx.json` declares the backend Rust canister and the static asset canister.

## Testing

The backend canister uses Rust. Build it with:

```bash
cargo check
```

Additional project-specific tests can be added under the `tests/` directory.
