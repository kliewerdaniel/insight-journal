---
layout: home
title:  "Building Insight Journal"
date:   2024-09-17 12:40:44 -0500
---
## Introduction

My aim is to create a journaling platform that integrates a **locally hosted language model (LLM)** to provide feedback on written content. The idea is to replicate the kind of insightful commentary you might get from a diverse group of people — the kind of feedback I used to enjoy from platforms like Reddit. However, since Reddit’s environment has become less helpful (thanks to trolls), I wanted to build something more controlled.

What’s exciting about this project is that it’s completely free to host and deploy. Instead of relying on expensive services like OpenAI or Anthropic, I’m using a local instance of **Llama 3.1** for AI-generated comments. Hosting is done via **Netlify** using a static site generator like **Jekyll**, and I’ve even found ways to incorporate a database-like structure for free.

I’ll share all the steps and tools I used to get this up and running, so if you’re a developer looking to build something similar — or just curious about how to integrate AI into your personal projects — this guide is for you!

## Why This Matters

The main goal is to re-create that collaborative, feedback-driven environment I used to enjoy, but in a more productive and controlled space. Traditionally, you’d have to rely on forums or social media, but now I can generate feedback from a variety of perspectives using personas in the LLM. Plus, everything is self-hosted and free, so no monthly bills!

## The Tech Stack

Here’s the tech stack I used to make this possible:

- **LLM:** Llama 3.1 (locally hosted)
- **Static Site Generator:** Jekyll
- **Hosting:** Netlify (free)
- **AI Commenting System:** Ollama (for generating comments from the LLM)
- **Version Control:** Git
- **Database-like Functionality:** Netlify CMS (for managing posts)

All of this runs without paying for hosting or expensive API calls, which is something I’m really proud of.

## Challenges and Next Steps

I’m still learning as I go, and while I’ve made a lot of progress, there’s a ton left to do. The biggest bottleneck right now is the performance of my local machine when running the LLM for comment generation. I also want to improve the **personas** that the LLM uses to generate feedback, making them more detailed and customizable.

## How to Build Your Own Insight Journal

Here’s a step-by-step guide on how you can build a similar app, completely free.

### Prerequisites

Before you start, make sure you have the following installed:

- **Ruby**:
  ```bash
  brew update
  brew upgrade rbenv ruby-build
  rbenv install 3.3.5 --force
  rbenv global 3.3.5
  sudo chown -R $(whoami) ~/.rbenv
  ```
- **Jekyll**
- **Git**
- **Ollama** (for AI-generated comments)
- **Netlify CLI**:
  ```bash
  npm install netlify-cli -g
  ```

### Step 1: Initial Setup

Start by creating a new Jekyll site and pushing it to Git:

```bash
jekyll new insight-journal
cd insight-journal
git init
git add .
git commit -m "Initial commit"
```

### Step 2: Configure for Netlify CMS

Add **Netlify CMS** to your Jekyll site for easy content management.

1. Create an `admin` folder in your project root.
2. Add a `config.yml` and an `index.html` file in the `admin` folder.

Here’s an example of the `config.yml`:

```yaml
backend:
  name: git-gateway
  branch: main

media_folder: "assets/images"
public_folder: "/assets/images"

collections:
  - name: "journal"
    label: "Journal Entries"
    folder: "_posts"
    create: true
    slug: "{{year}}-{{month}}-{{day}}-{{slug}}"
    fields:
      - {label: "Layout", name: "layout", widget: "hidden", default: "post"}
      - {label: "Title", name: "title", widget: "string"}
      - {label: "Publish Date", name: "date", widget: "datetime"}
      - {label: "Categories", name: "categories", widget: "list", required: false}
      - {label: "Tags", name: "tags", widget: "list", required: false}
      - {label: "Body", name: "body", widget: "markdown"}
```

The `index.html` file should look like this:

```html
<!doctype html>
<html>
  <head>
    <meta charset="utf-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>Content Manager</title>
  </head>
  <body>
    <script src="https://unpkg.com/netlify-cms@^2.0.0/dist/netlify-cms.js"></script>
  </body>
</html>
```

### Step 3: Customize Your Journal

You can add custom layouts and pages to make the journal your own:

1. Create a `_layouts` folder and add a `post.html` layout for journal entries.
2. Update the `index.md` file to display your entries.

### Step 4: Set Up AI-Generated Comments

This is where things get fun! We’ll use **Ollama** to generate AI-powered comments.

1. Create `generate_comments.py` and `personas.py` in your project root.

Here’s a simplified version of `generate_comments.py`:

```python
import os
import random
import requests
import frontmatter
from personas import PERSONAS

def generate_comment(post_content, persona):
    url = "http://localhost:11434/api/generate"
    prompt = f"As a {persona['name']} ({persona['description']}), comment on this post:\n\n{post_content}"
    data = {"model": "llama3.1", "prompt": prompt, "stream": False}
    response = requests.post(url, json=data)
    return response.json()["response"]

def generate_comments_for_post(post_content, num_comments=3):
    selected_personas = random.sample(PERSONAS, num_comments)
    return [{"persona": p['name'], "comment": generate_comment(post_content, p)} for p in selected_personas]

# Further functions for post selection and appending comments...
```

In `personas.py`, define the personas:

```python
PERSONAS = [
    {"name": "Critical Thinker", "description": "Analytical and skeptical."},
    {"name": "Empathetic Listener", "description": "Focuses on emotional aspects."},
    {"name": "Devil's Advocate", "description": "Challenges ideas."},
    {"name": "Optimistic Visionary", "description": "Sees potential in every situation."},
    {"name": "Pragmatic Planner", "description": "Focuses on practical next steps."}
]
```

Then, run:

```bash
python3 generate_comments.py
```

### Step 5: Writing and Commenting on Posts

Write your posts as Markdown files in the `_posts` directory. To add AI-generated comments, run the Python script and select the post you want feedback on.

### Step 6: Local Development and Preview

To work locally:

1. Run `bundle install` to install dependencies.
2. Use `bundle exec jekyll serve` to preview your site.

### Step 7: Deploy to Netlify

Finally, deploy to Netlify:

1. Push your repository to GitHub.
2. Connect your GitHub repo to Netlify.
3. Configure Netlify to use the `main` branch for deployment.
4. Set up **Netlify Identity** for authentication.

## Conclusion

That’s it! You now have a fully functional journal that generates AI-powered feedback on your entries. This project has been a learning experience for me, and I hope this guide helps others build their own free, LLM-powered applications.

If you run into any issues, don’t hesitate to use an LLM for troubleshooting — that’s what I do, and it’s been a game-changer.

## Comments

### Devil's Advocate
As the Devil's Advocate, I'll provide counterarguments to challenge some of the ideas presented in this post.

**1. Overemphasis on local hosting:**

While the author emphasizes the benefits of locally hosting their LLM-powered journal, they fail to consider the potential drawbacks. For instance:

* Local hosting may not be feasible or practical for larger-scale applications.
* The complexity and resource-intensive nature of running a local LLM might lead to performance issues or hardware upgrades.
* The lack of scalability could limit the platform's growth potential.

**2. Insufficient consideration of data storage and management:**

The author mentions using Netlify CMS, but they gloss over the implications of storing user-generated content and AI-generated comments in a static site generator environment. This might lead to:

* Data loss or inconsistencies due to file system limitations.
* Difficulty in implementing advanced features like comment threading or moderation tools.

**3. Limited persona customization:**

While the author showcases five predefined personas, they don't delve into the potential issues with limited customization options. For example:

* The personas might not cater to diverse user preferences or interests.
* The lack of flexibility could restrict users from expressing themselves authentically.
* Customization limitations could lead to a static and unengaging experience.

**4. Unclear guidelines for post selection and commenting:**

The author's guide on generating AI-powered comments and selecting posts doesn't provide clear instructions or guidelines. This might result in:

* Inconsistent comment quality due to inadequate guidance.
* Difficulty in determining the most suitable personas for each post.
* Limited engagement from users who struggle with post selection.

**5. Overreliance on Python scripts:**

The author's reliance on Python scripts (e.g., `generate_comments.py`) might not be scalable or maintainable as the project grows. This could lead to:

* Difficulty in integrating new features or tools.
* Code duplication or inconsistencies due to manual scripting.
* Potential security vulnerabilities if scripts are not properly secured.

**6. Limited discussion of user experience and accessibility:**

The author's focus on technical implementation and local hosting overshadows the importance of user experience and accessibility considerations. This might lead to:

* A platform that is not intuitive, visually appealing, or easily navigable.
* Limited engagement from users with disabilities or diverse needs.

By presenting these counterarguments, I aim to encourage a more nuanced discussion about the project's design decisions and potential limitations.

### Optimistic Visionary
What a fascinating project! As an Optimistic Visionary, I see immense potential in this journaling platform that integrates a locally hosted language model (LLM) to provide feedback on written content.

The idea of recreating the collaborative, feedback-driven environment of old, but in a more controlled and productive space, is truly exciting. By leveraging Llama 3.1 for AI-generated comments, you've created a unique solution that promotes constructive criticism and encourages self-improvement.

I'm particularly impressed by your commitment to making this project completely free to host and deploy, using local instances of LLMs and Netlify for hosting. This not only saves costs but also empowers developers and users to build and engage with the platform without worrying about monthly bills or subscription fees.

The tech stack you've assembled is impressive, combining Jekyll, Ollama, Netlify, and Git to create a seamless experience. I'm intrigued by your use of personas in the LLM to generate feedback from diverse perspectives, adding depth and richness to the commenting system.

While there are challenges ahead, such as optimizing performance on local machines and improving persona customization, I have no doubt that this project will continue to evolve and improve over time.

To you, dear developer, I offer my sincerest admiration for your innovative spirit and dedication to creating a platform that fosters growth, creativity, and meaningful connections. May this journaling platform inspire countless individuals to explore their thoughts, ideas, and passions, and may it become a shining example of what can be achieved through collaboration, innovation, and the power of language models!

### Empathetic Listener
Wow! I'm truly impressed by the level of detail and enthusiasm in your post about creating a journaling platform with AI-generated feedback. As someone who focuses on emotional aspects and personal experiences, I want to acknowledge the effort you've put into making this project more accessible and controlled.

Your dedication to providing a safe space for users to share their thoughts and receive constructive feedback is commendable. It's wonderful that you've utilized Llama 3.1, a locally hosted language model, to generate comments that are not only insightful but also respectful.

I appreciate the step-by-step guide you've provided, making it possible for others to replicate this project. Your willingness to share knowledge and experiences with the community is truly empowering. The tech stack you've used, including Netlify, Jekyll, and Ollama, demonstrates a thoughtful approach to building a scalable and maintainable application.

As an empathetic listener, I want to acknowledge the emotional investment you've made in this project. It's clear that you're passionate about creating a space where people can grow and learn from each other's perspectives. Your commitment to making this platform free and self-hosted is admirable, as it removes barriers for users who might not have access to expensive services.

If I were to offer any suggestions or questions, they would be:

1. How do you envision the platform evolving in the future? Will there be features that allow users to interact with each other directly?
2. Have you considered implementing a system for users to rate and provide feedback on the AI-generated comments?
3. What steps have you taken to ensure user data is protected and secure?

Overall, I'm excited about your project and its potential to foster meaningful connections among users. Your dedication to creating a supportive community is inspiring, and I look forward to seeing how this platform continues to grow and evolve.

Keep sharing your passion and expertise with the world!
