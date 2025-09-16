# 🕹️ Wikipedia Path Solver: Find shortest paths between 2 wiki pages

It's pretty fast in finding the shortest paths between Wikipedia articles.

**Try it out!**

- Visit [https://wikipathfinder.com/](https://wikipathfinder.com/)

This repo serves the backend api of this website Github: [https://github.com/binkybarnes/WikigameSolverWeb](https://github.com/binkybarnes/WikigameSolverWeb)

---

## Alternatives

I recently found several similar projects, here are some I found. (if you find other good ones make a request)

- [Six Degrees of Wikipedia](https://github.com/jwngr/sdow) — Probably the most popular inspiration for projects like this.
- [WikiPath ldobbelsteen/wikipath](https://github.com/ldobbelsteen/wikipath) — This one is fast, it uses LMDB, which also memory maps from disk. but for some paths it fails (e.g., Melvin T. Mason -> Goodenia iyouta). Probably has a safeguard that stops when visting too many nodes.
- [wikipath](https://github.com/wgoodall01/wikipath) — Also seems fast cause they loaded graph into memory, only local web ui however.
- [WikiGameSolver](https://github.com/spaceface777/WikiGameSolver) — Website doesn't seem to be working currently, but notable memory-loaded approach.
- [wikipath (Python & Rust)](https://github.com/cosmobobak/wikipath) — This one is interesting they use nlp stuff instead of graph.

This project is probably comparable to [WikiPath ldobbelsteen/wikipath](https://github.com/ldobbelsteen/wikipath) but they have other languages as well.

## 🛠️ Quick Start

If you want to run the webserver (the default option), you can run this. (the stuff at the beginning shows debug print statements)

```bash
RUST_LOG=debug,tracing_sqlx=warn cargo run --release
```

Once running, you can make search queries against the API:

### Example Request

**Endpoint:** `http://localhost:8000/api/search`
**Method:** `POST`
**Body:**

```json
{
  "start": "Eddie_Guerrero",
  "end": "Firefox",
  "output_as_ids": false
}
```

---

## Running Benchmark

To perform random searches for long paths, run:

```bash
cargo run --release -- --benchmark
```

It will do a minimal version of the search on 2 random pages and returns the path's length. It saves the longest paths it finds. On my computer I can get 12000 searches per second on 8 threads.

If you would like a version to find paths with a large number of paths, you can ask in an issue.

Press `Enter` to stop, at which point the results will be saved:

```text
running on 8 threads
Press Enter to stop BFS workers...

Total BFS runs: 63806
Threads used: 8
Top K global paths: 400000
Total BFS runs: 63806
Elapsed time: 8.92 seconds
BFS per second: 7150.72
paths 19: 1
paths 16: 1
paths 15: 1
paths 12: 2
paths 10: 2
paths 9: 25
paths 8: 302
paths 7: 1700
paths 6: 11472
paths 5: 29829
paths 4: 18142
paths 3: 2286
paths 2: 43
Encoding and saving to file
BFS finished. Results saved to results/top_paths_1758053059.bin
Elapsed: 8.93s
```

---

## Printing Benchmark Results

You can print benchmark results from a saved file with:

```bash
cargo run --release -- --print-benchmark results/top_paths_1757718671.bin
```

Output:

```text
Top 200 paths from file results/top_paths_1758054160.bin:
1. Depth 34: Two_Harbour_Green -> Billboard_Top_Hits:_1993
2. Depth 34: Diego_Daldosso -> 1884_Missouri_State_Auditor_election
3. Depth 33: Wendy_Williams:_What_a_Mess! -> 1880_Missouri_Secretary_of_State_election
4. Depth 31: Levantine_mansions_of_İzmir -> Billboard_Top_Hits:_1990
5. Depth 28: Jud_Conlon -> 1874_Missouri_State_Treasurer_election
6. Depth 23: Adobe_Photoshop_Lightroom_Classic_12.2_(Windows) -> 1896_Missouri_State_Treasurer_election
...
```

> The server is primarily a webserver, but the bidirectional BFS function can be used standalone.

# 🗂️ Preparing the Data

Before running the solver, you need to preprocess Wikipedia SQL dumps.  
Detailed instructions can be found in [DATA_PREP.md](./DATA_PREP.md).

## 🧩 How It Works

- **Graph Construction:**

  - From the SQL dump files, constructs a graph of the pagelinks in **CSR** form. Then serialize it to files.
  - Memory-maps those files on disk, otherwise it would take like 10gb of ram

- **Searching:**

  - Uses bidirectional BFS from both the start and end pages.

## 📚 Credits

- Data from [WikiMedia Dumps](https://dumps.wikimedia.org/enwiki/latest/) (page, pagelinks, linktarget, redirect SQL files)
- Inspiration from [Six Degrees of Wikipedia](https://github.com/jwngr/sdow)

---

```text
⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⣄⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀
⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⣠⣾⣿⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀
⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⣼⣿⣿⡟⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⣴⡟
⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⣼⢟⣝⣿⣇⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⢀⣼⡟⠀
⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⣠⣾⣿⣴⣭⣝⣿⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⢀⣾⡿⠀⠀
⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⣀⣤⣤⣴⡞⣛⣿⣿⣿⣿⣿⣿⣧⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⢠⣿⣿⠀⠀⠀
⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⢀⣀⣤⣴⣮⣽⣯⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣾⢷⣄⣀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⢀⣿⣿⠃⠀⠀⠀
⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⣠⠴⣖⣋⣽⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣟⣻⡷⢦⣄⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⢀⣾⣿⡿⠀⠀⠀⠀
⠀⠀⠀⠀⠀⠀⣀⣠⣶⣿⣷⢾⣿⣿⣿⣿⣿⣛⣿⢿⣿⣿⣿⣿⣿⣿⣽⣿⣿⠛⣿⣿⣿⣿⣿⣿⣷⡄⠉⢳⣦⣀⠀⠀⠀⠀⢀⡄⠀⠀⠀⢀⣾⣟⣿⠁⠀⠀⠀⠀
⠀⠀⢀⣠⣴⣾⣿⢿⣭⣉⣿⣿⣿⣿⣿⡿⠿⠿⠿⠿⢿⣿⠿⣿⢿⢻⣿⣿⣿⣷⣿⣿⡀⣯⢙⡻⣿⣿⣦⡈⣙⣿⣶⣤⣀⣤⣾⡁⠀⢀⣀⣾⣿⣿⡏⠀⠀⠀⠀⠀
⠰⠿⣿⡿⠿⣟⠻⣶⠉⠿⠟⠉⢉⣉⣤⠤⠶⠶⢤⠀⡀⠙⢀⡈⠎⠊⠃⢻⣿⣿⣿⣿⣿⣿⣾⣿⣿⣿⣿⣧⣼⡿⣿⣿⣿⣿⣿⣿⣿⣿⣿⠟⠃⢻⠀⠀⠀⠀⠀⠀
⠀⠀⠈⠻⠛⠛⠿⠥⠤⠶⠖⠊⠉⠉⠀⠀⠀⠀⠀⠀⠀⠀⠀⠁⠈⠀⠀⢿⠛⠿⣿⣿⣿⢿⡇⠀⠈⠉⠉⠻⠿⣷⣿⣿⣉⠻⡿⢛⣹⠟⠛⠳⣤⣿⣄⠀⠀⠀⠀⠀
⠀⠀⠀⠀⠀⠀⠀⠀⠀⠉⠓⠒⠲⠶⠶⠤⠤⠤⣤⠀⠀⠀⠀⠀⠀⠀⠈⠛⠷⣶⡾⣿⣿⣾⣻⣦⡀⠤⢄⣰⣂⣈⣿⣿⣿⣦⡙⠻⢧⠀⠀⠀⠉⡻⢿⠤⠀⠀⠀⠀
⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⢸⠂⠰⢦⣤⠽⠦⢤⣄⣀⣀⣀⣀⣲⣿⣿⣷⣹⣧⡙⠿⠿⣿⣿⠋⠛⠺⣿⡿⢷⣾⣇⠀⠀⠀⠈⠀⠀⠀⠀⠀⠀
⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⣸⠀⠀⡼⠃⠀⠀⠀⠀⠈⠉⠉⠉⠉⠉⢹⣿⣿⣿⣧⡀⠀⠈⠛⠀⠀⠀⣽⣿⣿⣏⣿⡆⠀⠀⠀⠀⠀⠀⠀⠀⠀
⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⣿⣤⣾⠇⢀⡀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⢻⣿⣿⣿⣧⠀⠀⠀⠀⠀⠺⢻⣿⣿⣧⣽⡿⠀⠀⠀⠀⠀⠀⠀⠀⠀
⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⢠⣾⡿⣿⣾⣰⢿⣧⡀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⢻⣿⣟⣿⠂⠀⠀⠀⠾⢿⣿⣿⡿⠿⠋⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀
⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⣠⣾⣿⡀⢈⣿⡿⣫⣿⡇⠀⠀⠀⠀⠀⠀⠀⠀⢀⣴⣾⣿⣿⣧⣤⣤⡀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀
⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⣠⣿⠟⢹⡇⠻⣫⣽⡿⢟⣛⣻⡀⠀⠀⠀⠀⠀⠀⣼⣿⠛⠻⢿⣿⣿⣿⡿⢷⡀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀
⠀⠀⠀⠀⠀⠀⠀⠀⢀⣀⣤⣤⡾⠛⠁⣠⣼⢿⣿⣵⡤⠴⠚⠋⠉⠀⠀⠀⠀⠀⢀⡴⠯⣌⠻⠿⢮⡭⣸⣿⣧⢾⣧⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀
⠀⠀⠀⠀⠀⠀⠀⠀⠘⠿⢿⣿⣿⣿⡿⠭⠚⠋⠁⠀⠀⠀⠀⠀⠀⠀⠀⠀⢀⣴⠛⠀⣀⠀⢀⣼⣿⣿⣿⠟⣡⣿⣿⡀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀
⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⣰⡟⠀⠀⠀⠀⠀⣼⠃⠛⠛⣡⣾⠟⢛⡁⣧⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀
⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⣀⣤⠞⣹⡇⢀⠉⠀⠀⠀⢸⣷⣴⠞⠉⢀⣠⡿⠟⠋⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀
⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⢠⣄⣾⡿⠷⠟⠋⠀⠀⢀⣀⣠⣶⠾⣋⣡⡶⠞⠋⠁⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀
⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠸⣿⣿⣶⣶⣶⣶⣶⣛⣋⣉⣩⠶⠟⠋⠁⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀
⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠉⠉⠉⠉⠉⠉⠉⠉⠁⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀
```

## 🖤
