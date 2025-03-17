# Technical Documentation: Esperanto Text (Kanji) Replacement and Ruby Annotation Tool

## Table of Contents

- [Technical Documentation: Esperanto Text (Kanji) Replacement and Ruby Annotation Tool](#technical-documentation-esperanto-text-kanji-replacement-and-ruby-annotation-tool)
  - [Table of Contents](#table-of-contents)
  - [Architecture Overview](#architecture-overview)
  - [Core Components](#core-components)
    - [1. Main Application (`main.py`)](#1-main-application-mainpy)
    - [2. Replacement Engine (`esp_text_replacement_module.py`)](#2-replacement-engine-esp_text_replacement_modulepy)
    - [3. JSON Generator (`esp_replacement_json_make_module.py`)](#3-json-generator-esp_replacement_json_make_modulepy)
    - [4. JSON Generation Page](#4-json-generation-page)
  - [Data Flow and Processing Pipeline](#data-flow-and-processing-pipeline)
  - [Text Processing and Replacement Engine](#text-processing-and-replacement-engine)
    - [Safe Replacement Mechanism](#safe-replacement-mechanism)
    - [Orchestration Function](#orchestration-function)
  - [Handling Esperanto Character Sets](#handling-esperanto-character-sets)
  - [Ruby Annotation System](#ruby-annotation-system)
    - [Size-Adjusted Ruby](#size-adjusted-ruby)
    - [Ruby Text Width Measurement](#ruby-text-width-measurement)
    - [Ruby Styling with CSS](#ruby-styling-with-css)
  - [Parallel Processing Implementation](#parallel-processing-implementation)
  - [JSON Generation System](#json-generation-system)
    - [JSON Structure](#json-structure)
    - [Generation Process](#generation-process)
  - [Advanced Features](#advanced-features)
    - [Placeholder System](#placeholder-system)
    - [Custom Word Stemming](#custom-word-stemming)
    - [Priority-Based Replacement](#priority-based-replacement)
  - [Code Structure and Organization](#code-structure-and-organization)

## Architecture Overview

This Streamlit application is designed to process Esperanto text by replacing words with corresponding translations or Kanji characters, and optionally adding Ruby annotations. The system architecture follows a modular design with clear separation of responsibilities:

- **User Interface Layer**: Implemented with Streamlit components in `main.py` and the JSON generation page
- **Processing Layer**: Core text processing logic in `esp_text_replacement_module.py`
- **Data Generation Layer**: JSON file creation utilities in `esp_replacement_json_make_module.py`
- **Data Storage**: External JSON and text files for storing replacement rules and placeholder strings

The application is structured around a central text processing pipeline that integrates various transformation functions. It also implements multiprocessing capabilities for handling large texts efficiently.

## Core Components

### 1. Main Application (`main.py`)

The main application serves as both the entry point and the orchestrator for the text replacement process:

```
main.py
│
├── UI Components
│   ├── Replacement JSON selection
│   ├── Input method options
│   ├── Output format settings
│   ├── Special character formatting
│   └── Advanced settings (parallel processing)
│
├── Data Loading
│   ├── load_replacements_lists() - Loads and caches JSON rules
│   └── import_placeholders() - Loads placeholder strings
│
└── Text Processing
    ├── Text input handling
    ├── Processing orchestration
    └── Result display and download options
```

Key functions in `main.py`:

- `load_replacements_lists()`: Loads and parses the JSON file containing replacement rules, using `@st.cache_data` for performance optimization
- `parallel_process()` or `orchestrate_comprehensive_esperanto_text_replacement()`: Processes the input text depending on whether parallel processing is enabled

### 2. Replacement Engine (`esp_text_replacement_module.py`)

This module forms the core text processing engine with functions for character conversion, text replacement, and processing orchestration:

```
esp_text_replacement_module.py
│
├── Character Conversion
│   ├── replace_esperanto_chars() - Generic conversion function
│   ├── convert_to_circumflex() - Converts to ĉ, ĝ, etc.
│   └── Character mapping dictionaries
│
├── Placeholder Processing
│   ├── safe_replace() - Core replacement function 
│   ├── find_percent_enclosed_strings_for_skipping_replacement()
│   └── find_at_enclosed_strings_for_localized_replacement()
│
├── Replacement Orchestration
│   └── orchestrate_comprehensive_esperanto_text_replacement()
│
└── Parallel Processing
    ├── process_segment() - Processes chunks of text
    └── parallel_process() - Manages multiprocessing logic
```

### 3. JSON Generator (`esp_replacement_json_make_module.py`)

This module contains utilities for creating replacement rule JSON files:

```
esp_replacement_json_make_module.py
│
├── Character Width Management
│   ├── measure_text_width_Arial16() - Measures text width in pixels
│   ├── insert_br_at_half_width() - Inserts line breaks for wide text
│   └── insert_br_at_third_width() - Divides text into thirds
│
├── Output Format Functions
│   └── output_format() - Formats text based on selected style
│
├── Ruby Handling
│   ├── capitalize_ruby_and_rt() - Capitalizes Ruby text
│   └── remove_redundant_ruby_if_identical() - Removes redundant Ruby
│
└── Parallel Processing
    ├── process_chunk_for_pre_replacements() - Processes data chunks
    └── parallel_build_pre_replacements_dict() - Parallel dictionary builder
```

### 4. JSON Generation Page

This component provides a UI for creating custom replacement JSON files:

```
JSON File Generation Page for Esperanto Text (Kanji) Replacement.py
│
├── UI Components
│   ├── CSV file selection/upload
│   ├── JSON configuration file selection
│   └── Parallel processing settings
│
├── Data Processing Components
│   ├── Word processing for different word forms
│   ├── Priority calculation for replacements
│   └── JSON structure assembly
│
└── Output Generation
    └── Combined JSON file creation and download
```

## Data Flow and Processing Pipeline

The application follows a multi-stage processing pipeline for text replacement:

1. **Input Collection**: Text is collected via manual input or file upload
2. **Configuration Loading**: Replacement rules and placeholders are loaded from files
3. **Text Pre-processing**: 
   - Spaces are normalized
   - Esperanto characters are converted to a standardized form
4. **Special Marker Processing**:
   - Text within `%...%` is identified and set aside for skipping replacement
   - Text within `@...@` is identified for localized replacement
5. **Main Replacement Process**:
   - Global replacement of words using `replacements_final_list`
   - Two-character root replacement using `replacements_list_for_2char`
   - Restoration of specially marked text segments
6. **Post-processing**:
   - Format-specific adjustments (HTML, Ruby annotations, etc.)
   - Character format conversion based on user choice
7. **Output Generation**: 
   - Displaying the results
   - Providing download functionality

## Text Processing and Replacement Engine

### Safe Replacement Mechanism

The core of the text replacement engine is the `safe_replace()` function, which uses a two-step replacement process to avoid unintended replacements when strings overlap:

```python
def safe_replace(text: str, replacements: List[Tuple[str, str, str]]) -> str:
    """
    (old, new, placeholder) のリストを受け取り、
    text中の old → placeholder → new の段階置換を行う。
    """
    valid_replacements = {}
    # まず old→placeholder
    for old, new, placeholder in replacements:
        if old in text:
            text = text.replace(old, placeholder)
            valid_replacements[placeholder] = new
    # 次に placeholder→new
    for placeholder, new in valid_replacements.items():
        text = text.replace(placeholder, new)
    return text
```

This function:
1. Takes a list of tuples `(old, new, placeholder)`
2. First replaces all occurrences of `old` with the unique `placeholder`
3. Tracks which placeholders were actually used in a `valid_replacements` dictionary
4. Then replaces all used placeholders with their corresponding `new` value

This approach prevents problems that would occur if the `new` strings contained parts of other `old` strings, which could lead to cascading replacements and incorrect results.

### Orchestration Function

The `orchestrate_comprehensive_esperanto_text_replacement()` function coordinates the entire text processing pipeline:

```python
def orchestrate_comprehensive_esperanto_text_replacement(
    text, 
    placeholders_for_skipping_replacements: List[str],
    replacements_list_for_localized_string: List[Tuple[str, str, str]],
    placeholders_for_localized_replacement: List[str],
    replacements_final_list: List[Tuple[str, str, str]],
    replacements_list_for_2char: List[Tuple[str, str, str]],
    format_type: str
) -> str:
    # 1, 2) 空白の正規化 + エスペラント字上符への変換
    text = unify_halfwidth_spaces(text)
    text = convert_to_circumflex(text)
    
    # 3) %...% スキップ部の一時置換
    replacements_list_for_intact_parts = create_replacements_list_for_intact_parts(text, placeholders_for_skipping_replacements)
    sorted_replacements_list_for_intact_parts = sorted(replacements_list_for_intact_parts, key=lambda x: len(x[0]), reverse=True)
    for original, place_holder_ in sorted_replacements_list_for_intact_parts:
        text = text.replace(original, place_holder_)
    
    # 4) @...@ 局所置換
    tmp_replacements_list_for_localized_string_2 = create_replacements_list_for_localized_replacement(
        text, placeholders_for_localized_replacement, replacements_list_for_localized_string
    )
    sorted_replacements_list_for_localized_string = sorted(tmp_replacements_list_for_localized_string_2, key=lambda x: len(x[0]), reverse=True)
    for original, place_holder_, replaced_original in sorted_replacements_list_for_localized_string:
        text = text.replace(original, place_holder_)
    
    # 5) 大域置換 (old, new, placeholder)
    # ... [implementation continues] ...
```

This function is the integration point for all text processing steps and ensures they are executed in the correct order.

## Handling Esperanto Character Sets

The application supports multiple formats for Esperanto's special characters and allows conversion between them:

1. **Circumflex notation** (ĉ, ĝ, ĥ, ĵ, ŝ, ŭ)
2. **X-notation** (cx, gx, hx, jx, sx, ux)
3. **Hat notation** (c^, g^, h^, j^, s^, u^)

These conversions are managed using mapping dictionaries and specialized functions:

```python
x_to_circumflex = {
    'cx': 'ĉ', 'gx': 'ĝ', 'hx': 'ĥ', 'jx': 'ĵ', 'sx': 'ŝ', 'ux': 'ŭ',
    'Cx': 'Ĉ', 'Gx': 'Ĝ', 'Hx': 'Ĥ', 'Jx': 'Ĵ', 'Sx': 'Ŝ', 'Ux': 'Ŭ'
}
# ... other mapping dictionaries ...

def replace_esperanto_chars(text, char_dict: Dict[str, str]) -> str:
    for original_char, converted_char in char_dict.items():
        text = text.replace(original_char, converted_char)
    return text

def convert_to_circumflex(text: str) -> str:
    text = replace_esperanto_chars(text, hat_to_circumflex)
    text = replace_esperanto_chars(text, x_to_circumflex)
    return text
```

The application standardizes input to circumflex notation for internal processing, then converts to the user's preferred format for output.

## Ruby Annotation System

The application supports adding Ruby annotations (furigana-like text above words) using HTML's `<ruby>` tag. The implementation includes several key features:

### Size-Adjusted Ruby

For better readability, the application can adjust Ruby text size based on the width ratio between the main text and the Ruby text:

```python
def output_format(main_text, ruby_content, format_type, char_widths_dict):
    if format_type == 'HTML格式_Ruby文字_大小调整':
        width_ruby = measure_text_width_Arial16(ruby_content, char_widths_dict)
        width_main = measure_text_width_Arial16(main_text, char_widths_dict)
        ratio_1 = width_ruby / width_main
        if ratio_1 > 6:
            return f'<ruby>{main_text}<rt class="XXXS_S">{insert_br_at_third_width(ruby_content, char_widths_dict)}</rt></ruby>'
        elif ratio_1 > (9/3):
            return f'<ruby>{main_text}<rt class="XXS_S">{insert_br_at_half_width(ruby_content, char_widths_dict)}</rt></ruby>'
        # ... more conditions ...
```

The function:
1. Calculates the width ratio between ruby text and main text
2. Assigns CSS classes (XXS_S, XS_S, etc.) based on the ratio
3. Inserts line breaks for very long ruby text

### Ruby Text Width Measurement

The application measures text width in pixels using a pre-calculated dictionary of character widths:

```python
def measure_text_width_Arial16(text, char_widths_dict: Dict[str, int]) -> int:
    total_width = 0
    for ch in text:
        char_width = char_widths_dict.get(ch, 8)
        total_width += char_width
    return total_width
```

This allows for accurate sizing and line break decisions without rendering the text.

### Ruby Styling with CSS

The HTML output includes CSS for styling the Ruby text:

```python
ruby_style_head="""<!DOCTYPE html>
<html lang="ja">
  <head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>大多数の环境中で正常に运行するRuby显示功能</title>
    <style>
    html, body {
      -webkit-text-size-adjust: 100%;
      -moz-text-size-adjust: 100%;
      -ms-text-size-adjust: 100%;
      text-size-adjust: 100%;
    }
  
      :root {
        --ruby-color: blue;
        --ruby-font-size: 0.5em;
      }
      /* ... more styles ... */
```

The CSS provides responsive sizing, proper alignment, and color styling for Ruby annotations.

## Parallel Processing Implementation

For handling large texts efficiently, the application implements parallel processing using Python's `multiprocessing` module:

```python
def parallel_process(
    text: str,
    num_processes: int,
    placeholders_for_skipping_replacements: List[str],
    replacements_list_for_localized_string: List[Tuple[str, str, str]],
    placeholders_for_localized_replacement: List[str],
    replacements_final_list: List[Tuple[str, str, str]],
    replacements_list_for_2char: List[Tuple[str, str, str]],
    format_type: str
) -> str:
    # ... implementation ...
    
    # 行ごとに分割 (改行込み)
    lines = re.findall(r'.*?\n|.+$', text)
    num_lines = len(lines)
    # ... more implementation ...
    
    with multiprocessing.Pool(processes=num_processes) as pool:
        results = pool.starmap(
            process_segment,
            [
                (
                    lines[start:end],
                    placeholders_for_skipping_replacements,
                    replacements_list_for_localized_string,
                    placeholders_for_localized_replacement,
                    replacements_final_list,
                    replacements_list_for_2char,
                    format_type
                )
                for (start, end) in ranges
            ]
        )
    return ''.join(results)
```

Key aspects of the implementation:

1. The text is split into lines using regex
2. Lines are divided into chunks based on the specified number of processes
3. Each chunk is processed in parallel using `multiprocessing.Pool`
4. Results are joined back together in the correct order

The application also handles the multiprocessing start method to avoid issues on different platforms:

```python
try:
    multiprocessing.set_start_method("spawn")
except RuntimeError:
    pass  # すでに start method が設定済みの場合はここで無視する
```

## JSON Generation System

The JSON generation component is a key feature that allows users to create custom replacement rules:

### JSON Structure

The generated JSON contains three main components:

1. **Global replacement list** (`replacements_final_list`): Rules for replacing Esperanto words throughout the text
2. **Two-character root replacement list** (`replacements_list_for_2char`): Rules for common Esperanto affixes and short roots
3. **Localized string replacement list** (`replacements_list_for_localized_string`): Rules for replacing strings only in @...@ contexts

### Generation Process

The JSON generation process involves several stages:

1. **Data Loading**: Reading from CSV files containing Esperanto roots and their translations
2. **Dictionary Creation**: Building dictionaries for various replacement types
3. **Word Form Processing**: Handling different forms of words (e.g., with suffixes)
4. **Priority Calculation**: Assigning replacement priorities based on word length and type
5. **Case Handling**: Creating variants for uppercase, lowercase, and capitalized forms
6. **Special Processing**: Handling verb endings, suffixes, etc.
7. **Final Assembly**: Creating the final JSON structure

A key function in this process is `parallel_build_pre_replacements_dict()`, which constructs the replacement dictionaries in parallel:

```python
def parallel_build_pre_replacements_dict(
    E_stem_with_Part_Of_Speech_list: List[List[str]],
    replacements: List[Tuple[str, str, str]],
    num_processes: int = 4
) -> Dict[str, List[str]]:
    # ... implementation ...
    
    with multiprocessing.Pool(num_processes) as pool:
        partial_dicts = pool.starmap(
            process_chunk_for_pre_replacements,
            [(chunk, replacements) for chunk in chunks]
        )
    
    # Merge results
    merged_dict = {}
    for partial_d in partial_dicts:
        for E_root, val in partial_d.items():
            # ... merging logic ...
    
    return merged_dict
```

## Advanced Features

### Placeholder System

The application uses a sophisticated placeholder system to ensure accurate text replacement:

1. **Global placeholders**: Used during the main replacement process
2. **Two-character root placeholders**: Used for short Esperanto roots and affixes
3. **Local replacement placeholders**: Used for text within @...@ markers
4. **Skipping placeholders**: Used for text within %...% markers

These placeholders are imported from external text files:

```python
placeholders_for_skipping_replacements: List[str] = import_placeholders(
    './Appの运行に使用する各类文件/占位符(placeholders)_%1854%-%4934%_文字列替换skip用.txt'
)
```

### Custom Word Stemming

The JSON generation tool supports custom stemming rules through a JSON configuration file:

```python
for i in custom_stemming_setting_list:
    if len(i)==3:
        try:
            esperanto_Word_before_replacement = i[0].replace('/', '')
            if i[1] == "dflt":
                replacement_priority_by_length = len(esperanto_Word_before_replacement)*10000
            # ... more processing ...
```

This allows users to define how Esperanto words should be broken into roots and how they should be processed.

### Priority-Based Replacement

The application uses a priority system to ensure longer words are replaced before shorter ones:

```python
pre_replacements_list_2 = sorted(pre_replacements_list_1, key=lambda x: x[2], reverse=True)
```

This prevents issues where partial replacements might interfere with complete word replacements.

## Code Structure and Organization

The application code is organized across four main files:

1. **main.py**: The main Streamlit application entry point (466 lines)
2. **JSON File Generation Page...**: The JSON generation page (547 lines)
3. **esp_text_replacement_module.py**: Core text processing module (342 lines)
4. **esp_replacement_json_make_module.py**: JSON generation utilities (366 lines)

The code follows several design patterns:

- **Module pattern**: Functionality is organized into cohesive modules
- **Strategy pattern**: Different processing strategies can be selected at runtime
- **Factory pattern**: Various objects (like replacement lists) are created through factory functions
- **Pipeline pattern**: Text processing follows a sequential pipeline of transformations

The application also makes extensive use of Python's type hints for better code documentation and IDE support:

```python
def load_replacements_lists(json_path: str) -> Tuple[List, List, List]:
```

---

This technical documentation provides a comprehensive overview of the Esperanto Text (Kanji) Replacement and Ruby Annotation Tool's internal architecture and implementation. It covers the key components, data flow, algorithms, and special features that make this application function.