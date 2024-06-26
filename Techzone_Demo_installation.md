Absolutely! Here's the Markdown file you requested, properly formatted with headings, code blocks, and clear organization:

# Resources

Github Repository: [https://github.com/instructlab](https://github.com/instructlab)

Techzone Registration: [https://techzone.ibm.com/collection/tech-zone-certified-base-images/journey-instructlab-beta](https://techzone.ibm.com/collection/tech-zone-certified-base-images/journey-instructlab-beta)

InstructLab Hands On: [https://gen-ai-platform.tech-patterns.techzone.ibm.com/use-cases/ai_engineering/instructlab-hands-on/?](https://gen-ai-platform.tech-patterns.techzone.ibm.com/use-cases/ai_engineering/instructlab-hands-on/?)


## Basic Installation

1. **Go to:** [https://techzone.ibm.com/collection/tech-zone-certified-base-images/journey-instructlab-beta](https://techzone.ibm.com/collection/tech-zone-certified-base-images/journey-instructlab-beta)
2. Click on "reserve".
3. Download the key provided in your reservation.
4. Save it under `~/.ssh`.
5. Secure the file with: `chmod 600 <filename>.pem`

## Go to:

[https://gen-ai-platform.tech-patterns.techzone.ibm.com/use-cases/ai_engineering/instructlab-hands-on/?](https://gen-ai-platform.tech-patterns.techzone.ibm.com/use-cases/ai_engineering/instructlab-hands-on/?)

## Open a Terminal and Access the Provisioned Environment via SSH

**First Window:**

```bash
ssh -i [key.pem] itzuser@[ipaddress] -p2223
sudo bash
cd /instructlab
source venv/bin/activate
ilab serve --model-path models/[model filename]
```

**Second Window:**

```bash
ssh -i [key.pem] itzuser@[ipaddress] -p2223
sudo bash
cd /instructlab
source venv/bin/activate
ilab chat
```

To find the model path, use: `ls models`

## Alignment (Model Finetuning)

**Prerequisites:** Go to `/instructlab/`

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

> [!NOTE]
> If you want to pickup from here and generate training data, that can be used to train the models, refer to https://github.com/instructlab/instructlab?tab=readme-ov-file#-list-and-validate-your-new-data