# InstructLab Local Installation Guide (Mac)

This guide will walk you through installing and using the InstructLab Command-Line Interface (CLI) on your Mac to interact with AI models and train them on your own custom data.

## Resources
Github Repository: [https://github.com/instructlab](https://github.com/instructlab)

## Prerequisites

* **Python:** Ensure you have Python 3.8 or higher installed. You can check by running `python3 --version` in your terminal.
* **Pip:** The Python package installer should also be available (`pip3 --version`).
* **Git:** This is used to clone the InstructLab repository. Install it if you don't have it already.
* **Virtual Environment (Recommended):** Create a dedicated virtual environment to prevent dependency conflicts.

## Installation Steps

1. **Clone the Repository:**

   ```bash
   git clone https://github.com/instructlab/instructlab.git
   cd instructlab
   ```

2. **Create and Activate Virtual Environment (Optional but Recommended):**

   ```bash
    python3 -m venv --upgrade-deps venv
    source venv/bin/activate
    pip cache remove llama_cpp_python
    pip install instructlab 
   ```

4. **From your `venv` environment, verify `ilab` is installed correctly, by running the `ilab` command.**

   ```shell
   ilab
   ```

   *Example output of the `ilab` command*

   ```shell
   (venv) $ ilab
   Usage: ilab [OPTIONS] COMMAND [ARGS]...

   CLI for interacting with InstructLab.

   If this is your first time running InstructLab, it's best to start with `ilab init` to create the environment.

   Options:
   --config PATH  Path to a configuration file.  [default: config.yaml]
   --version      Show the version and exit.
   --help         Show this message and exit.

   Commands:
   chat      Run a chat using the modified model
   check     (Deprecated) Check that taxonomy is valid
   convert   Converts model to GGUF
   diff      Lists taxonomy files that have changed since <taxonomy-base>...
   download  Download the model(s) to train
   generate  Generates synthetic data to enhance your example data
   init      Initializes environment for InstructLab
   list      (Deprecated) Lists taxonomy files that have changed since <taxonomy-base>.
   serve     Start a local server
   test      Runs basic test to ensure model correctness
   train     Takes synthetic data generated locally with `ilab generate`...
   ```

   > **IMPORTANT:** every `ilab` command needs to be run from within your Python virtual environment. To enter the Python environment, run the following command:

   ```shell
   source venv/bin/activate
   ```
### 🏗️ Initialize `ilab`

1. Initialize `ilab` by running the following command:

   ```shell
   ilab init
   ```

   *Example output*

   ```shell
   Welcome to InstructLab CLI. This guide will help you set up your environment.
   Please provide the following values to initiate the environment [press Enter for defaults]:
   Path to taxonomy repo [taxonomy]: <ENTER>
   ```

2. When prompted by the interface, press **Enter** to add a new default `config.yaml` file.

3. When prompted, clone the `https://github.com/instructlab/taxonomy.git` repository into the current directory by typing **y**.

   **Optional**: If you want to point to an existing local clone of the `taxonomy` repository, you can pass the path interactively or alternatively with the `--taxonomy-path` flag.

   *Example output after initializing `ilab`*

   ```shell
   (venv) $ ilab init
   Welcome to InstructLab CLI. This guide will help you set up your environment.
   Please provide the following values to initiate the environment [press Enter for defaults]:
   Path to taxonomy repo [taxonomy]: <ENTER>
   `taxonomy` seems to not exists or is empty. Should I clone https://github.com/instructlab/taxonomy.git for you? [y/N]: y
   Cloning https://github.com/instructlab/taxonomy.git...
   Generating `config.yaml` in the current directory...
   Initialization completed successfully, you're ready to start using `ilab`. Enjoy!
   ```

   `ilab` will use the default configuration file unless otherwise specified. You can override this behavior with the `--config` parameter for any `ilab` command.

### 📥 Download the model

- Run the `ilab download` command.

  ```shell
  ilab download
  ```

  `ilab download` downloads a compact pre-trained version of the [model](https://huggingface.co/instructlab/) (~4.4G) from HuggingFace and store it in a `models` directory:

  ```shell
  (venv) $ ilab download
  Downloading model from instructlab/merlinite-7b-lab-GGUF@main to models...
  (venv) $ ls models
  merlinite-7b-lab-Q4_K_M.gguf
  ```

  > **NOTE** ⏳ This command can take few minutes or immediately depending on your internet connection or model is cached. If you have issues connecting to Hugging Face, refer to the [Hugging Face discussion forum](https://discuss.huggingface.co/) for more details.

### 🍴 Serving the model

- Serve the model by running the following command:

   ```shell
   ilab serve
   ```

- Serve a non-default model (e.g. Mixtral-8x7B-Instruct-v0.1):

   ```shell
   ilab serve --model-path models/mixtral-8x7b-instruct-v0.1.Q4_K_M.gguf
   ```

- Once the model is served and ready, you'll see the following output:

   ```shell
   (venv) $ ilab serve
   INFO 2024-03-02 02:21:11,352 lab.py:201 Using model 'models/ggml-merlinite-7b-lab-Q4_K_M.gguf' with -1 gpu-layers and 4096 max context size.
   Starting server process
   After application startup complete see http://127.0.0.1:8000/docs for API.
   Press CTRL+C to shut down the server.
   ```

   > **NOTE:** If multiple `ilab` clients try to connect to the same InstructLab server at the same time, the 1st will connect to the server while the others will start their own temporary server. This will require additional resources on the host machine.

## 💻 Creating new knowledge or skills and training the model

### 🎁 Contribute knowledge or compositional skills

1. Contribute new knowledge or compositional skills to your local [taxonomy](https://github.com/instructlab/taxonomy.git) repository.

Detailed contribution instructions can be found in the [taxonomy repository](https://github.com/instructlab/taxonomy/blob/main/README.md).

**Create the Necessary Directory Structure:**

```bash
mkdir -p taxonomy/knowledge/textbook/culture/movies/awards/oscars
mkdir -p taxonomy/knowledge/textbook/culture/movies/awards/golden_globes_movies 
```

**Create and Write to the qna.yaml File for the Oscars:**

```bash
cat << EOF > taxonomy/knowledge/textbook/culture/movies/awards/oscars/qna.yaml
task_description: 'Teach the model the results of the 2024 Oscars'
created_by: juliadenham
domain: pop_culture
seed_examples:
 - question: When did the 2024 Oscars happen?
   answer: |
     The 2024 Oscars were held on March 10, 2024.
 - question: What film had the most Oscar nominations in 2024?
   answer: |
     Oppenheimer had 13 Oscar nominations.
 - question: Who presented the 2024 Oscar for Best Original Screenplay and Best Adapted Screenplay?
   answer: |
     Octavia Spencer presented the award for Best Original Screenplay and Best Adapted Screenplay at the 2024 Oscars.
 - question: Who hosted the 2024 Oscars?
   answer: |
     Jimmy Kimmel hosted the 96th Academy Awards ceremony.
 - question: At the 2024 Oscars, who were the nominees for best director and who won?
   answer: |
     The nominees for director at the 2024 Oscars was Christopher Nolan for Oppenheimer,
     Justine Triet for Anatomy of a Fall, Martin Scorsese for Killers of the Flower Moon,
     Yorgos Lanthimos for Poor Things, and Jonathan Glazer for The Zone of Interest.
     Christopher Nolan won best director for Oppenheimer.
 - question: Did Billie Eilish perform at the 2024 Oscars?
   answer: |
     Yes Billie Eilish performed "What Was I Made For?" from Barbie at the 2024 Oscars.
document:
 repo: https://github.com/juliadenham/oscars2024_knowledge.git
 commit: e1744af
 patterns:
   - oscars2024_results.md
EOF
```

**Create and Write to the attribution.txt File for the Oscars:**

```bash
cat << EOF > taxonomy/knowledge/textbook/culture/movies/awards/oscars/attribution.txt
Title of work: 96th Academy Awards
Link to work: https://en.wikipedia.org/wiki/96th_Academy_Awards
License of the work: CC-BY-SA-4.0
Creator names: Wikipedia Authors
EOF
```

### 📜 List and validate your new data

1. List your new data by running the following command:

   ```shell
   ilab diff
   ```

2. To ensure `ilab` is registering your new knowledge or skills, you can run the `ilab diff` command. The following is the expected result after adding the new compositional skill `foo-lang`:

   ```shell
   (venv) $ ilab diff
   compositional_skills/writing/freeform/foo-lang/foo-lang.yaml
   Taxonomy in /taxonomy/ is valid :)
   ```
### 🚀 Generate a synthetic dataset

Before following these instructions, ensure the existing model you are adding skills or knowledge to is still running.

1. To generate a synthetic dataset based on your newly added knowledge or skill set in [taxonomy](https://github.com/instructlab/taxonomy.git) repository, run the following command:

   ```shell
   ilab generate
   ```

   Use a non-default model (e.g. Mixtral-8x7B-Instruct-v0.1) to generate data, run the following command:

   ```shell
   ilab generate --model models/mixtral-8x7b-instruct-v0.1.Q4_K_M.gguf
   ```

   > **NOTE:** ⏳ This can take from 15 minutes to 1+ hours to complete, depending on your computing resources.

   *Example output of `ilab generate`*

   ```shell
   (venv) $ ilab generate
   INFO 2024-02-29 19:09:48,804 lab.py:250 Generating model 'ggml-merlinite-7b-lab-Q4_K_M' using 10 CPUs,
   taxonomy: '/home/username/instructlab/taxonomy' and seed 'seed_tasks.json'

   0%|##########| 0/100 Cannot find prompt.txt. Using default prompt.
   98%|##########| 98/100 INFO 2024-02-29 20:49:27,582 generate_data.py:428 Generation took 5978.78s
   ```

   The synthetic data set will be three files in the newly created `generated` directory named `generated*.json`, `test*.jsonl`, and `train*.jsonl`.

> [!NOTE]
> If you want to pickup from where a failed or canceled `ilab generate` left off, you can copy the
> `generated*.json` file into a file named `regen.json`. `regen.json` will be picked up at the start of `lab
> generate` when available. You should remove it when the process is completed.

2. Verify the files have been created by running the `ls generated` command.

   ```shell
   (venv) $ ls generated/
   'generated_ggml-merlinite-7b-lab-0226-Q4_K_M_2024-02-29T19 09 48.json'   'train_ggml-merlinite-7b-lab-0226-Q4_K_M_2024-02-29T19 09 48.jsonl'
   'test_ggml-merlinite-7b-lab-0226-Q4_K_M_2024-02-29T19 09 48.jsonl'
   ```

   **Optional**: It is also possible to run the generate step against a different model via an
   OpenAI-compatible API. For example, the one spawned by `ilab serve` or any remote or locally hosted LLM (e.g. via [`ollama`](https://ollama.com/), [`LM Studio`](https://lmstudio.ai), etc.). Run the following command:

   ```shell
   ilab generate --endpoint-url http://localhost:8000/v1
   ```

### 👩‍🏫 Training the model

There are many options for training the model with your synthetic data-enhanced dataset.

> **Note:** **Every** `ilab` command needs to run from within your Python virtual environment.

#### Train the model locally on an M-series Mac

To train the model locally on your M-Series Mac is as easy as running:

```shell
ilab train
```

> **Note:** ⏳ This process will take a little while to complete (time can vary based on hardware
and output of `ilab generate` but on the order of 5 to 15 minutes)

`ilab train` outputs a brand-new model that is saved in the `<model_name>-mlx-q` directory called `adapters.npz` (in `Numpy` compressed array format). For example:

```shell
(venv) $ ls instructlab-merlinite-7b-lab-mlx-q
adapters-010.npz        adapters-050.npz        adapters-090.npz        config.json             tokenizer.model
adapters-020.npz        adapters-060.npz        adapters-100.npz        model.safetensors       tokenizer_config.json
adapters-030.npz        adapters-070.npz        adapters.npz            special_tokens_map.json
adapters-040.npz        adapters-080.npz        added_tokens.json       tokenizer.jso
```

### 📜 Test the newly trained model

- Run the following command to test the model:

   ```shell
   ilab test
   ```

   > **NOTE:** 🍎 This step is only implemented for macOS with M-series chips (for now)

   The output from the command will consist of a series of outputs from the model before and after training.

2. Convert the newly trained model by running the following command:

   ```shell
   ilab convert
   ```

3. Serve the newly trained model locally via `ilab serve` command with the `--model-path`
argument to specify your new model:

   ```shell
   ilab serve --model-path <new model path>
   ```

   Which model should you select to serve? After running the `ilab convert` command, some files and a directory are generated. The model you will want to serve ends with an extension of `.gguf`
   and exists in a directory with the suffix `trained`. For example:
   `instructlab-merlinite-7b-lab-trained/instructlab-merlinite-7b-lab-Q4_K_M.gguf`.

## 📣 Chat with the new model (not optional this time)

- Try the fine-tuned model out live using the chat interface, and see if the results are better than the untrained version of the model with chat by running the following command:

   ```shell
   ilab chat -m <New model name>
   ```

   If you are interested in optimizing the quality of the model's responses, please see [`TROUBLESHOOTING.md`](./TROUBLESHOOTING.md#model-fine-tuning-and-response-optimization)

## 🚀 Upgrade InstructLab to latest version

- To upgrade InstructLab to the latest version, use the following command:

   ```shell
   pip install instructlab --upgrade
   ```

