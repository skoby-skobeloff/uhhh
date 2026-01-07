import requests
from datetime import datetime
import time
import random

PLACE_ID = "2753915549"  # Blox Fruits
BASE_URL = "https://games.roblox.com/v1/games"

headers = {
    "User-Agent": "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/121.0.0.0 Safari/537.36",
    "Accept": "application/json"
}

oldest_match = None
cursor = ""

def smart_sleep():
    wait = 9 + random.uniform(-2, 2)  # 7–11 sec
    print(f"Sleeping {wait:.2f}s")
    time.sleep(wait)

while True:
    url = f"{BASE_URL}/{PLACE_ID}/servers/Public?sortOrder=Asc&limit=100"
    if cursor:
        url += f"&cursor={cursor}"

    # keep retrying if rate limited
    while True:
        r = requests.get(url, headers=headers)
        if r.status_code == 429:
            print("429 hit, chilling...")
            smart_sleep()
            continue
        elif r.status_code != 200:
            print("Error fetching page:", r.status_code)
            exit()
        break

    data = r.json()
    servers = data.get("data", [])

    if not servers:
        print("No servers returned on this page")
        break

    for s in servers:
        raw = s.get("updated")
        if not raw:
            continue

        dt = datetime.fromisoformat(raw.replace("Z", "+00:00"))

        if oldest_match is None or dt < oldest_match[1]:
            oldest_match = (s, dt)

    cursor = data.get("nextPageCursor")
    if not cursor:
        break

    smart_sleep()

if oldest_match:
    server, dt = oldest_match
    print("\n=== RESULT ===")
    print("Oldest server ID:", server.get("id"))
    print("Updated:", dt)
    print("Players:", server.get("playing"), "/", server.get("maxPlayers"))
else:
    print("No servers found (rate limit or empty response)")
