---
title: FoundationAgents/OpenManus: No fortress, purely open ground. OpenManus is Coming.
---

# FoundationAgents/OpenManus: No fortress, purely open ground. OpenManus is Coming.

url:: https://github.com/FoundationAgents/OpenManus?tab=readme-ov-file
up: [[sources]]

#source

[![](https://github.com/FoundationAgents/OpenManus/raw/main/assets/logo.jpg)](https://github.com/FoundationAgents/OpenManus/blob/main/assets/logo.jpg)

English | [中文](https://github.com/FoundationAgents/OpenManus/blob/main/README_zh.md) | [한국어](https://github.com/FoundationAgents/OpenManus/blob/main/README_ko.md) | [日本語](https://github.com/FoundationAgents/OpenManus/blob/main/README_ja.md)

[![GitHub stars](https://camo.githubusercontent.com/f8914c5eb0aebcfe318fd5b7972dbc13d7a4fec7725aa35f5416ff7d9e473901/68747470733a2f2f696d672e736869656c64732e696f2f6769746875622f73746172732f6d616e6e61616e64706f656d2f4f70656e4d616e75733f7374796c653d736f6369616c)](https://github.com/mannaandpoem/OpenManus/stargazers)   [![License: MIT](https://camo.githubusercontent.com/6cd0120cc4c5ac11d28b2c60f76033b52db98dac641de3b2644bb054b449d60c/68747470733a2f2f696d672e736869656c64732e696f2f62616467652f4c6963656e73652d4d49542d79656c6c6f772e737667)](https://opensource.org/licenses/MIT)   [![Discord Follow](https://camo.githubusercontent.com/d016b445b8b73609e6357b96197fda0c36aed31be020640652c0bdc8490ee26b/68747470733a2f2f646362616467652e76657263656c2e6170702f6170692f7365727665722f44596e323977466b397a3f7374796c653d666c6174)](https://discord.gg/DYn29wFk9z) [![Demo](https://camo.githubusercontent.com/8d327f67c31977ebdce10a71eccaab001f8cebab5a2331dc7bbc2630cd4e1b8d/68747470733a2f2f696d672e736869656c64732e696f2f62616467652f44656d6f2d48756767696e67253230466163652d79656c6c6f77)](https://huggingface.co/spaces/lyh-917/OpenManusDemo) [![DOI](https://camo.githubusercontent.com/38c79d06b7c1fa6ecb1a0f9c64a814c6d67820bfa450244270ebec8758f0bf83/68747470733a2f2f7a656e6f646f2e6f72672f62616467652f444f492f31302e353238312f7a656e6f646f2e31353138363430372e737667)](https://doi.org/10.5281/zenodo.15186407)

Manus is incredible, but OpenManus can achieve any idea without an *Invite Code* 🛫!

Our team members [@Xinbin Liang](https://github.com/mannaandpoem) and [@Jinyu Xiang](https://github.com/XiangJinyu) (core authors), along with [@Zhaoyang Yu](https://github.com/MoshiQAQ), [@Jiayi Zhang](https://github.com/didiforgithub), and [@Sirui Hong](https://github.com/stellaHSR), we are from [@MetaGPT](https://github.com/geekan/MetaGPT). The prototype is launched within 3 hours and we are keeping building!

It's a simple implementation, so we welcome any suggestions, contributions, and feedback!

Enjoy your own agent with OpenManus!

We're also excited to introduce [OpenManus-RL](https://github.com/OpenManus/OpenManus-RL), an open-source project dedicated to reinforcement learning (RL)- based (such as GRPO) tuning methods for LLM agents, developed collaboratively by researchers from UIUC and OpenManus.
seo_website.mp4

We provide two installation methods. Method 2 (using uv) is recommended for faster installation and better dependency management.

1. Create a new conda environment:

```
conda create -n open_manus python=3.12
conda activate open_manus
```

2. Clone the repository:

```
git clone https://github.com/mannaandpoem/OpenManus.git
cd OpenManus
```

3. Install dependencies:

```
pip install -r requirements.txt
```

### Method 2: Using uv (Recommended)

[](#method-2-using-uv-recommended)

1. Install uv (A fast Python package installer and resolver):

```
curl -LsSf https://astral.sh/uv/install.sh | sh
```

2. Clone the repository:

```
git clone https://github.com/mannaandpoem/OpenManus.git
cd OpenManus
```

3. Create a new virtual environment and activate it:

```
uv venv --python 3.12
source .venv/bin/activate  # On Unix/macOS
# Or on Windows:
# .venv\Scripts\activate
```

4. Install dependencies:

```
uv pip install -r requirements.txt
```

### Browser Automation Tool (Optional)

[](#browser-automation-tool-optional)

OpenManus requires configuration for the LLM APIs it uses. Follow these steps to set up your configuration:

1. Create a `config.toml` file in the `config` directory (you can copy from the example):

```
cp config/config.example.toml config/config.toml
```

2. Edit `config/config.toml` to add your API keys and customize settings:

```
# Global LLM configuration
[llm]
model = "gpt-4o"
base_url = "https://api.openai.com/v1"
api_key = "sk-..."  # Replace with your actual API key
max_tokens = 4096
temperature = 0.0

# Optional configuration for specific LLM models
[llm.vision]
model = "gpt-4o"
base_url = "https://api.openai.com/v1"
api_key = "sk-..."  # Replace with your actual API key
```

One line for run OpenManus:

Then input your idea via terminal!

For MCP tool version, you can run:

For unstable multi-agent version, you also can run:

We welcome any friendly suggestions and helpful contributions! Just create issues or submit pull requests.

Or contact @mannaandpoem via 📧email: [mannaandpoem@gmail.com](mailto:mannaandpoem@gmail.com)

**Note** : Before submitting a pull request, please use the pre-commit tool to check your changes. Run `pre-commit run --all-files` to execute the checks.

Join our networking group on Feishu and share your experience with other developers!  
[![OpenManus 交流群](https://github.com/FoundationAgents/OpenManus/raw/main/assets/community_group.jpg)](https://github.com/FoundationAgents/OpenManus/blob/main/assets/community_group.jpg)

[![Star History Chart](https://camo.githubusercontent.com/ce7b989d3a88773a341d37ffc7ee77449a697ecd50acddd19f1cd74111db71c4/68747470733a2f2f6170692e737461722d686973746f72792e636f6d2f7376673f7265706f733d6d616e6e61616e64706f656d2f4f70656e4d616e757326747970653d44617465)](https://star-history.com/#mannaandpoem/OpenManus&Date)

Thanks to [PPIO](https://ppinfra.com/user/register?invited_by=OCPKCN&utm_source=github_openmanus&utm_medium=github_readme&utm_campaign=link) for computing source support.
> PPIO: The most affordable and easily-integrated MaaS and GPU cloud solution.

Thanks to [anthropic-computer-use](https://github.com/anthropics/anthropic-quickstarts/tree/main/computer-use-demo) and [browser-use](https://github.com/browser-use/browser-use) for providing basic support for this project!

Additionally, we are grateful to [AAAJ](https://github.com/metauto-ai/agent-as-a-judge), [MetaGPT](https://github.com/geekan/MetaGPT), [OpenHands](https://github.com/All-Hands-AI/OpenHands) and [SWE-agent](https://github.com/SWE-agent/SWE-agent).

We also thank stepfun(阶跃星辰) for supporting our Hugging Face demo space.

OpenManus is built by contributors from MetaGPT. Huge thanks to this agent community!  

```
@misc{openmanus2025,
  author = {Xinbin Liang and Jinyu Xiang and Zhaoyang Yu and Jiayi Zhang and Sirui Hong and Sheng Fan and Xiao Tang},
  title = {OpenManus: An open-source framework for building general AI agents},
  year = {2025},
  publisher = {Zenodo},
  doi = {10.5281/zenodo.15186407},
  url = {https://doi.org/10.5281/zenodo.15186407},
}
```

