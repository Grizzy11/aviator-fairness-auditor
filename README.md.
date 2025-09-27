import hashlib
import csv
import json
import streamlit as st
import pandas as pd
from io import StringIO

# --- Core Functions ---

def verify_server_seed(server_seed: str, published_hash: str) -> (bool, str):
    computed_hash = hashlib.sha256(server_seed.encode()).hexdigest()
    return computed_hash == published_hash, computed_hash

def aviator_crash_point(server_seed: str, client_seeds: list) -> (float, str):
    merged = server_seed + ''.join(client_seeds)
    hash_hex = hashlib.sha512(merged.encode()).hexdigest()
    h = int(hash_hex[:16], 16)

    if int(hash_hex, 16) % 101 == 0:
        return 1.00, hash_hex

    crash_point = (100 * 2**52) / h
    return max(1.0, round(crash_point, 2)), hash_hex

def process_rounds(rounds_df, check_only=False):
    audit_data = []

    for _, row in rounds_df.iterrows():
        published = str(row["published_hash"]).strip()
        server = str(row["server_seed"]).strip()
        clients = [str(row["client1"]).strip(), str(row["client2"]).strip(), str(row["client3"]).strip()]
        round_id = str(row.get("round_id", len(audit_data)+1))

        # Verify server
        server_ok, computed_hash = verify_server_seed(server, published)

        # Crash point
        crash_point, sha512_hash = (None, None)
        if server_ok and not check_only:
            crash_point, sha512_hash = aviator_crash_point(server, clients)

        audit_data.append({
            "round_id": round_id,
            "published_hash": published,
            "server_seed": server,
            "server_seed_hash_computed": computed_hash,
            "server_seed_verified": server_ok,
            "client1": clients[0],
            "client2": clients[1],
            "client3": clients[2],
            "sha512_merged_hash": sha512_hash,
            "crash_point": crash_point,
        })

    return audit_data

# --- Streamlit App ---

st.set_page_config(page_title="Aviator Fairness Auditor", layout="wide")

st.title("✈️ Aviator Provably Fair Auditor")
st.write("Upload your rounds CSV to verify seeds and calculate crash points.")

uploaded_file = st.file_uploader("Upload Rounds CSV", type=["csv"])

check_only = st.checkbox("Check-only mode (skip crash calculation)", value=False)

if uploaded_file:
    # Load CSV into DataFrame
    stringio = StringIO(uploaded_file.getvalue().decode("utf-8"))
    rounds_df = pd.read_csv(stringio)

    # Process
    audit_data = process_rounds(rounds_df, check_only)
    results_df = pd.DataFrame(audit_data)

    st.subheader("✅ Verification Results")
    st.dataframe(results_df)

    # CSV download
    csv_out = results_df.to_csv(index=False).encode("utf-8")
    st.download_button(
        label="⬇️ Download Results CSV",
        data=csv_out,
        file_name="aviator_results.csv",
        mime="text/csv"
    )

    # JSON download
    json_out = json.dumps(audit_data, indent=2).encode("utf-8")
    st.download_button(
        label="⬇️ Download Audit JSON",
        data=json_out,
        file_name="aviator_audit.json",
        mime="application/json"
    )
