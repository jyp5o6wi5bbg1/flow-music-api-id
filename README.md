# Flow Music API (`flowmusic`)

<!-- conv-kit:v1 -->

<p align="center">
  <img src="assets/badges/price.svg" alt="observed unit price"> <img src="assets/badges/billing.svg" alt="billing model"> <img src="assets/badges/compat.svg" alt="OpenAI-compatible endpoint">
</p>

<p align="center">
  <img src="assets/audio-waveform.svg" width="820" alt="Music generation request path through the APIMart audio endpoint">
</p>

> **from $0.01 per generation** on the APIMart route — satu endpoint yang kompatibel dengan OpenAI di `https://api.apimart.ai/v1`, tanpa paket bulanan. *(diamati 2026-10-12)*

**[Dapatkan API key](https://go.apimart.ai/k-cec15c)** · **[Harga live](https://go.apimart.ai/k-2fa3b0)** · **[Halaman model](https://go.apimart.ai/k-a244bb)** · [⚡ Quickstart 60 detik](#quickstart)

**Alasan tim memakai Flow Music (`flowmusic`) lewat APIMart**

- **Satu key untuk seluruh katalog.** base URL `https://api.apimart.ai/v1` dan header `Authorization` yang sama menjangkau Flow Music (`flowmusic`) plus 300+ model gambar, video, dan bahasa lainnya — cukup ganti field `model`, bukan klien Anda.
- **Minimum $1, bayar sesuai pemakaian.** Tanpa langganan dan tanpa paket prabayar yang harus dihitung di awal: isi saldo mulai $1 dan pakai untuk panggilan.
- **Biaya dikembalikan di respons.** Setiap task yang selesai melaporkan jumlah yang ditagih (`cost` / `credits_cost`); task berstatus `failed` dikembalikan dananya.
- **Asinkron sejak awal.** Kirim, ambil `task_id`, polling `GET /v1/tasks/{id}` — batching dan retry hanyalah pekerjaan antrean biasa, bukan integrasi khusus.

<!-- /conv-kit:v1 -->



Flow Music API on APIMart provides a production-ready AI music generation workflow for creating full songs from lyrics and style prompts, with vocals or instrumental output and asynchronous task polling.

## Model id and routes

| Route | `model` value | Billing | Notes |
| --- | --- | --- | --- |
| Music generation (async task) | `flowmusic` | per generated track, by operation | see the [model page](https://go.apimart.ai/k-a244bb) for the exact tiers |

Endpoint: `POST https://api.apimart.ai/v1/music/generations`, then poll `GET /v1/tasks/{task_id}`.

## Pricing

<!-- pricing:model:start -->
| Resolution | List price | Effective price |
| --- | --- | --- |
| default | 0.075 | 0.06 |


<!-- pricing:model:end -->

Prices are a snapshot; the [pricing page](https://go.apimart.ai/k-2fa3b0) and [`data/model.json`](data/model.json) are refreshed by CI, and a completed task reports the exact amount in its `cost` field.

## Request parameters

| Field | Type | Default | Notes |
| --- | --- | --- | --- |
| model | string | — | the music model id |
| title | string | — | track title |
| sound_prompt | string | — | style / instrumentation description (one of sound_prompt or lyrics is required) |
| lyrics | string | — | lyrics text with section tags; leave empty for instrumental |
| bpm | string | number | — | tempo of the generated track |
| length | integer | — | target duration in seconds |
| audio_url | string | — | source audio for extend / cover / stem operations |
| callback_url | string | — | webhook called when the task completes |

The table lists the fields accepted by the APIMart `music/generations` endpoint. Check the [model page](https://go.apimart.ai/k-a244bb) for anything this model adds on top.

## Quickstart

```bash
curl --request POST --url https://api.apimart.ai/v1/music/generations \
  --header "Authorization: Bearer $APIMART_API_KEY" --header 'Content-Type: application/json' \
  -d '{"model":"flowmusic","title":"My Song","sound_prompt":"upbeat pop music with piano","bpm":"120","length":60}'
```

```python
import os, time, requests

BASE = "https://api.apimart.ai/v1"
HEADERS = {"Authorization": f"Bearer {os.environ['APIMART_API_KEY']}", "Content-Type": "application/json"}

created = requests.post(f"{BASE}/music/generations", headers=HEADERS, timeout=60, json={
    "model": "flowmusic", "title": "My Song", "sound_prompt": "upbeat pop music with piano", "bpm": "120", "length": 60,
}).json()
task_id = created["data"]["id"]

while True:
    task = requests.get(f"{BASE}/tasks/{task_id}", headers=HEADERS, timeout=60).json()["data"]
    if task["status"] in ("completed", "failed"):
        break
    time.sleep(5)
print(task.get("cost"), task["result"]["audio"][0]["url"])
```

Runnable versions: [`examples/`](examples). The task lifecycle is `pending → processing → completed | failed`.

## FAQ

**Apa id model Flow Music?**

`flowmusic`. Lihat tabel rute di atas.

**Bagaimana penagihannya?**

**from $0.01 per generation** on the APIMart route. Task yang selesai melaporkan jumlah tepatnya di field `cost`; task yang gagal dikembalikan dananya.

**How do I get a key?**

Use the [Get an API key](https://go.apimart.ai/k-cec15c) link — $1 minimum top-up, no plan required.

<!-- related-repos:auto -->
## Related repositories

- **Same model, this language** — the other half of this pair: [flow-music-prompts-id](https://github.com/jyp5o6wi5bbg1/flow-music-prompts-id)
- **Same model, other languages** — 10 more language builds: English `flow-music-api` · 中文 `flow-music-api-cn` · Deutsch `flow-music-api-de` · Español `flow-music-api-es` · Français `flow-music-api-fr` · Relay/gateway `flow-music-api-gateway` · 日本語 `flow-music-api-ja` · 한국어 `flow-music-api-ko` · Português `flow-music-api-pt` · Русский `flow-music-api-ru`
- **More models in this language** — [midjourney-api-id](https://github.com/cvrwq0/midjourney-api-id) · [gemini-2-5-flash-image-preview-api-id](https://github.com/gmrg0s/gemini-2-5-flash-image-preview-api-id) · [nano-banana-api-id](https://github.com/vsa185x9ecrd/nano-banana-api-id) · [gemini-3-1-flash-image-preview-api-id](https://github.com/p61qrgpg/gemini-3-1-flash-image-preview-api-id) · [nano-banana-2-api-id](https://github.com/n1rf4ezzz/nano-banana-2-api-id) · [gemini-3-pro-image-preview-api-id](https://github.com/s78ve5v4x9ji2/gemini-3-pro-image-preview-api-id)
- **Topic hubs** — every build of this model, all languages: [flow-music](https://github.com/topics/flow-music) · [flow-music-api](https://github.com/topics/flow-music-api)

This repository is one build in a cross-linked set of API guides for the same model family; each language build links back to the others.
<!-- /related-repos:auto -->

## Attributed links (how this repository is measured)

| Purpose | Attributed link | Target |
| --- | --- | --- |
| Model page | <https://go.apimart.ai/k-a244bb> | `apimart.ai/id/model/flow-music` |
| Live pricing | <https://go.apimart.ai/k-2fa3b0> | `apimart.ai/id/pricing` |
| Get an API key | <https://go.apimart.ai/k-cec15c> | `apimart.ai/id/keys` |

Outbound APIMart links are minted through the promo link API; hand-made tracking parameters are rejected by
`tools/check_links.py` in CI.

## Disclosure

Flow Music is a third-party model served through APIMart; this repository documents how to call it and publishes
model ids and prices, and does not claim official status. Model names, prices and documentation belong to their
respective owners.

## License

MIT — see [LICENSE](LICENSE).
