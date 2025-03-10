### AI Notes

- Model size and quantization: [Good article](https://smcleod.net/2024/07/understanding-ai/llm-quantisation-through-interactive-visualisations/)
    - Main takeaway: run at Q4_K_M if possible, otherwise at Q3_K_M

- Ollama 
    - Documentation
    - [Model library](https://ollama.com/library)
    - [Ollama copilot](https://github.com/bernardo-bruning/ollama-copilot)
    - download a particular size model: <model-name>:<size>, e.g. `ollama pull granite3.1-dense:8b`
    - download a particular quantization type
    - Set context size
    - Model use cases:
        - Coding:
            - Codellama (7B, 13B, 34B)
            - WizardCoder (15B)
        - Documentation writing 
            -   


- Coding assistant in VScode
    - Continue plugin
        - [Article](https://developer.ibm.com/tutorials/awb-local-ai-copilot-ibm-granite-code-ollama-continue/) using IBM granite (good for resources, but tab completions doesn't seem to work)


- AI Literature
    - [Wolfram's article](https://writings.stephenwolfram.com/2023/02/what-is-chatgpt-doing-and-why-does-it-work/)