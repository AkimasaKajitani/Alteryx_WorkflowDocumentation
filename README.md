# Alteryx Workflow Documentation with LLM

[For Japanese user](https://github.com/AkimasaKajitani/Alteryx_WorkflowDocumentation/blob/main/README_ja.md)

## What is this project?

This project aims to create a tool that automatically generates documentation for Alteryx workflows. Since Alteryx workflows are essentially XML files, their contents can be analyzed to understand what the workflow is doing. Specifically, by passing this XML to an LLM like Gemini, the logic can be interpreted automatically. However, to manage LLM costs, this project follows a policy of extracting as much information as possible directly from the XML via parsing, leaving only the high-level interpretation to the LLM.

While Alteryx Copilot offers a "Summarize Workflow" feature (found in the top right of the UI), it isn't quite designed for generating structured documentation, which is why this project was started.

For comparison, here is how "Summarize Workflow" describes the main workflow of this

>This workflow processes Alteryx workflow files (.yxmd, .yxmc, .yxwz) to create detailed reports. It extracts metadata, categorizes tools, analyzes input/output, extracts Python code, and generates descriptions using an LLM. The final output is a comprehensive PDF report for each workflow, including an image of the canvas.

As you can see, it provides a high-level overview, but it might feel a bit brief. If you need more detail or want the output as a PDF or Word report, this project will be useful.

## Report Samples
The following samples were generated using Google Gemini.
- [Weekly Challenge 447 solution file](https://github.com/AkimasaKajitani/Alteryx_WorkflowDocumentation/blob/main/sample_report/report_Challenge_447_solution_file.yxmd.pdf)
    - An example solution for [Weekly Challenge 447](https://community.alteryx.com/t5/Weekly-Challenges/Challenge-447-Analysis-of-Survey-Results-Delivered-in-a/td-p/1331307) on the Alteryx Community. It analyzes survey results from a Jupyter Notebook.
- [Advent of Code 2025 Day04](https://github.com/AkimasaKajitani/Alteryx_WorkflowDocumentation/blob/main/sample_report/report_Day04.yxmd.pdf)
    - A workflow solving [Day 4](https://adventofcode.com/2025/day/4) of [Advent Of Code](https://adventofcode.com/) 2025. This involves finding the number of accessible "@" symbols on an X,Y grid.
- [Project Euler Problem 003](https://github.com/AkimasaKajitani/Alteryx_WorkflowDocumentation/blob/main/sample_report/report_Problem_0003.yxmd.pdf)
    - A workflow solving [Problem 3](https://projecteuler.net/problem=3) of [Project Euler](https://projecteuler.net/about). It calculates the largest prime factor of the number 600,851,475,143.

## Features
- Generates reports consisting of the following sections:
    - Workflow Basic Information
        - General info, Author info, Metadata, and Detailed settings.
    - Tool Statistics
        - Count of tools and tool types, Category statistics.
    - Input/Output
        - Input and Output summaries.
    - Workflow Canvas Image
    - Workflow Overview
    - Python Code
- Bilingual Support: LLM-generated workflow descriptions are available in both English and Japanese.

## Prerequisites
- Data Privacy: Please be aware that the workflow body is sent to the LLM. Information contained within .yxmd/.yxmc/.yxwz files will be transmitted.
    - Raw data is not sent, but metadata (such as field names) and formulas will be included in the prompt.
- LLM Access: This tool uses either the GenAI tool or a dedicated Gemini API macro.
    - An active LLM subscription/contract is required.
- Model Compatibility: Developed primarily using Google Gemini (2.5 Flash). Using other LLMs or models may result in unexpected behavior.
- File Setup: Workflows you wish to document should be saved in the folder specified in the Directory tool.
- Canvas Images: You must extract icons for the workflow canvas images.
- Python Packages: Requires the following:
    - networkx
    - Pillow

## Installation
Please download the following files from GitHub. Extract the .yxzp file using Alteryx, and unzip the zip file using your preferred extraction tool.

### Common
- [icon.zip](https://github.com/AkimasaKajitani/Alteryx_WorkflowDocumentation/blob/main/icons.zip)
- [install_python_pkg.yxmd](https://github.com/AkimasaKajitani/Alteryx_WorkflowDocumentation/blob/main/install_python_pkg.yxmd) 
    - [日本語版](https://github.com/AkimasaKajitani/Alteryx_WorkflowDocumentation/blob/main/install_python_pkg_ja.yxmd)

### GenAI Version
- [DocumentationWorkflow_GenAI.yxzp](https://github.com/AkimasaKajitani/Alteryx_WorkflowDocumentation/blob/main/DocumentationWorkflow_GenAI.yxzp)

### Gemini Version
- [DocumentationWorkflow_Gemini.yxzp](https://github.com/AkimasaKajitani/Alteryx_WorkflowDocumentation/blob/main/DocumentationWorkflow_Gemini.yxzp)


## How to Use
### Install Additional Python Packages
Start Alteryx Designer with Administrator privileges, open the workflow, and run it to install the required Python packages. If you are unfamiliar with this, right-click Designer and select "Run as Administrator," then execute install_python_pkg.yxmd.

### GenAI Version Preparation
1. Create an LLM Connection in Alteryx One (Admin privileges required).
    - Using a high-speed model is recommended.
2. Configure the LLM Override tool to use your LLM connection.

### Gemini Version Preparation
1. Obtain a Gemini API key from Google AI Studio.
2. Enter the API key into the Gemini Macro settings.

### Common Steps
1. Extract and place workflow icon files into an icon folder located directly inside the folder where your workflows are saved.
2. Save the files you want to document in a workflow folder (or update the Directory tool configuration to point to your desired folder).
3. Set the language in the Text Input tool (ID 300). The default is English. To use Japanese, set the enabled column for the ja record to 1 and leave the en record blank.
4. Run the workflow.

## Important Notes
- Expect a wait time during the LLM processing phase. Additionally, creating the canvas image via the Python tool takes a bit of time.
- LIf you wish to skip the LLM processing, check the Detour Tool (ID 276) to divert the flow to the right.
- If you see the following error, you have reached the LLM rate limit. Please reduce the number of workflows processed simultaneously:
    >"Warning: Prompt (279): Error occurred in LLM response generation: 429 Client Error: Too Many Requests for url: https://us1.alteryxcloud.com/aims/v1/generatedContent "
- For very large workflows, some LLMs or models may fail to process the request or stop outputting mid-way. Please consider switching models in such cases.

## Report Sections & Implementation
| No | Section | Method |
|:---|:----|:-------|
| 1 | Workflow Basic Information | XML Parsing |
| 2 | Tool Statistics | XML Parsing |
| 3 | Input/Output | XML Parsing |
| 4 | Workflow Canvas Image | Image generated via Python based on parsed XML data |
| 5 | Workflow Detail Description | Sent (nearly) entire XML file to LLM |
| 6 | Python Code | XML Parsing |

## Unimplemented Features
- LLM Cost Optimization: Only partially implemented (e.g., removing comment tool backgrounds and macro icons).
- Tool Statistics: Detailed settings for each tool are not currently extracted. Pie chart colors do not match category colors (limitation of the Interactive Chart tool).
- Workflow Canvas Image: Comment tool text and macro icons are not displayed.
- Code Extraction: Code from the R Tool is not currently extracted.

## LLM Implementation Reference
- The workflow XML is passed to the LLM almost as-is.
- The goal is to eventually compress the transmitted data while maintaining output quality.
- Tasks that can be done via XML parsing are handled locally to save on LLM costs and stabilize output.
- The LLM output is requested in JSON format.
- In Gemini, providing separate "System Instructions" and "User Prompts" in different languages sometimes caused duplicate outputs. Therefore, all instructions are currently included in the main prompt body.
- While LLMs default to Markdown, Alteryx handles JSON more effectively for downstream reporting tools to style the final document.

## History
| Date | Version | Description |
|:-----|:--------|:-------|
| 2026/03/21 | 1.00 | Initial Release |

## Contact Indormation
Alteryx Community ([AkimasaKajitani](https://community.alteryx.com/t5/user/viewprofilepage/user-id/10936))
