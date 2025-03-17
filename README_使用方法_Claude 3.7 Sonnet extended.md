# User Guide: Esperanto Text (Kanji) Replacement and Ruby Annotation Tool

## Table of Contents

- [User Guide: Esperanto Text (Kanji) Replacement and Ruby Annotation Tool](#user-guide-esperanto-text-kanji-replacement-and-ruby-annotation-tool)
  - [Table of Contents](#table-of-contents)
  - [Introduction](#introduction)
  - [Getting Started](#getting-started)
  - [Main Application: Text Replacement](#main-application-text-replacement)
    - [Loading Replacement Rules](#loading-replacement-rules)
    - [Input Methods](#input-methods)
    - [Output Format Options](#output-format-options)
    - [Special Character Options](#special-character-options)
    - [Special Formatting Syntax](#special-formatting-syntax)
    - [Advanced Settings](#advanced-settings)
    - [Processing and Results](#processing-and-results)
  - [JSON Generation Tool](#json-generation-tool)
    - [Creating Custom Replacement Rules](#creating-custom-replacement-rules)
    - [Step-by-Step Guide](#step-by-step-guide)
  - [Examples and Use Cases](#examples-and-use-cases)
    - [Example 1: Basic Text Replacement](#example-1-basic-text-replacement)
    - [Example 2: Using Skip Markers](#example-2-using-skip-markers)
    - [Example 3: Using Local Replacement](#example-3-using-local-replacement)
  - [Troubleshooting](#troubleshooting)
    - [Common Issues](#common-issues)
    - [Getting Help](#getting-help)

## Introduction

The **Esperanto Text (Kanji) Replacement and Ruby Annotation Tool** is a specialized application that allows you to:

1. Convert Esperanto text by replacing words with Kanji characters or other translations
2. Add Ruby annotations (small text above characters, similar to furigana in Japanese)
3. Create custom replacement rules through a JSON file generation tool

This application is particularly useful for:
- Language learners who want to visualize Esperanto roots with their translations
- Teachers creating educational materials that show word components visually
- Anyone interested in seeing the connections between Esperanto and other languages

## Getting Started

The application consists of two main components:

1. **Main Page (Text Replacement Tool)**: Where you input Esperanto text and get the converted output
2. **JSON Generation Page**: Where you can create custom replacement rules

When you first launch the application, you'll see the main Text Replacement Tool. You can navigate to the JSON Generation Tool through the sidebar menu if you need to create custom replacement rules.

## Main Application: Text Replacement

### Loading Replacement Rules

The first step is to select the JSON file containing replacement rules:

1. Choose one of two options:
   - **Use the default JSON file**: Uses the built-in replacement rules
   - **Upload a JSON file**: Upload your own custom replacement rules

   ![Replacement JSON Selection](https://placeholder-image.com/replacement-json-selection.png)

2. If you need a sample JSON file, expand the "Sample JSON" section and download the provided file.

### Input Methods

You can provide Esperanto text in two ways:

1. **Enter text manually**: Type or paste text directly into the text area
2. **Upload a text file**: Upload a UTF-8 encoded text file (.txt, .csv, or .md)

   ![Input Method Selection](https://placeholder-image.com/input-method-selection.png)

### Output Format Options

Select the output format that best suits your needs:

1. **HTML Ruby with size adjustment**: Creates HTML with Ruby annotations where the Ruby text size adjusts based on length
2. **HTML Ruby with size adjustment + Kanji replacement**: Similar to above, but with the Kanji as the main text and Esperanto as Ruby
3. **HTML format only**: Basic HTML with Ruby annotations (fixed size)
4. **HTML format + Kanji replacement**: Basic HTML with Esperanto as Ruby
5. **Parentheses format**: Simple format with translations in parentheses
6. **Parentheses format + Kanji replacement**: Simple format with Esperanto in parentheses
7. **Replace with Kanji (no markup), text only**: Plain text with Kanji only, no annotations

   ![Output Format Selection](https://placeholder-image.com/output-format-selection.png)

### Special Character Options

For Esperanto's special characters (ĉ, ĝ, ĥ, etc.), choose the output style:

1. **Use superscript notation**: Displays characters with circumflex (ĉ, ĝ, etc.)
2. **Use x notation**: Displays characters in x-notation (cx, gx, etc.)
3. **Use ^ notation**: Displays characters with caret (c^, g^, etc.)

   ![Character Style Selection](https://placeholder-image.com/character-style-selection.png)

### Special Formatting Syntax

The application supports special syntax for more control over replacements:

1. **%...% (Skip replacement)**: Any text enclosed in percentage signs will **not** be replaced
   - Example: `Mi %amas% vin` - The word "amas" will remain as is
   - Limit: Up to 50 characters between the % signs

2. **@...@ (Local replacement)**: Only the text enclosed in @ signs will be replaced
   - Example: `Mi volas manĝi @pomon@` - Only "pomon" will be replaced
   - Limit: Up to 18 characters between the @ signs

### Advanced Settings

For processing large texts, you can enable parallel processing:

1. Expand the "Advanced Settings (Parallel Processing)" section
2. Check "Enable parallel processing" if desired
3. Adjust the number of parallel processes (2-4 recommended)

   ![Advanced Settings](https://placeholder-image.com/advanced-settings.png)

### Processing and Results

After configuring all options:

1. Click the **Submit** button to process your text
2. The results will appear in tabs below:
   - **HTML Preview**: For HTML output formats, shows the rendered result
   - **Replacement Result**: Shows the raw output (HTML source or plain text)
3. Click the **Download the replacement result** button to save the output

   ![Processing Results](https://placeholder-image.com/processing-results.png)

## JSON Generation Tool

The JSON Generation Tool allows you to create custom replacement rules. This is useful if you want to:
- Use different translations for Esperanto roots
- Add new Esperanto roots not covered in the default file
- Customize how words are segmented and displayed

### Creating Custom Replacement Rules

The JSON file contains three main components:

1. **Global replacement list**: Rules for replacing Esperanto words throughout the text
2. **Localized string replacement list**: Rules for replacing strings only in @...@ contexts
3. **2-character root replacement list**: Rules for common Esperanto affixes and short roots

### Step-by-Step Guide

1. Navigate to the JSON Generation Page (from the sidebar)

2. **Step 1: Prepare Your CSV File**
   - Choose whether to upload a custom CSV or use the default
   - The CSV must contain Esperanto roots and their translations (see sample files for format)

3. **Step 2: Prepare the JSON Files**
   - Choose whether to upload custom JSON files or use defaults for:
     - Esperanto word-stemming rules
     - Custom replacement strings
   - These files determine how words are broken into roots and how they're replaced

4. **Step 3: Configure Advanced Settings**
   - Enable parallel processing if needed for large datasets
   - Set the number of processes

5. **Create and Download the JSON File**
   - Click "Create the replacement JSON file" button
   - Wait for processing to complete
   - Download the generated file when prompted

6. Use this generated JSON file in the main application by selecting "Upload a JSON file" option

## Examples and Use Cases

### Example 1: Basic Text Replacement

**Input:**
```
La rapida ruĝa vulpo saltas super la pigra hundo.
```

**Output (HTML Ruby format):**
```html
<ruby>La<rt>The</rt></ruby> <ruby>rapid<rt>fast</rt></ruby><ruby>a<rt>adj</rt></ruby> <ruby>ruĝ<rt>red</rt></ruby><ruby>a<rt>adj</rt></ruby> <ruby>vulp<rt>fox</rt></ruby><ruby>o<rt>noun</rt></ruby> <ruby>salt<rt>jump</rt></ruby><ruby>as<rt>present</rt></ruby> <ruby>super<rt>over</rt></ruby> <ruby>la<rt>the</rt></ruby> <ruby>pigr<rt>lazy</rt></ruby><ruby>a<rt>adj</rt></ruby> <ruby>hund<rt>dog</rt></ruby><ruby>o<rt>noun</rt></ruby>.
```

### Example 2: Using Skip Markers

**Input:**
```
Mi %amas% lerni Esperanton.
```

**Output:**
```html
<ruby>Mi<rt>I</rt></ruby> amas <ruby>lern<rt>learn</rt></ruby><ruby>i<rt>inf</rt></ruby> <ruby>Esperant<rt>Esperanto</rt></ruby><ruby>o<rt>noun</rt></ruby><ruby>n<rt>acc</rt></ruby>.
```

### Example 3: Using Local Replacement

**Input:**
```
La knabo @manĝas@ pomon.
```

**Output:**
```html
La knabo <ruby>manĝ<rt>eat</rt></ruby><ruby>as<rt>present</rt></ruby> pomon.
```

## Troubleshooting

### Common Issues

1. **Text not being replaced correctly**
   - Check if the Esperanto word exists in your replacement JSON
   - Ensure you're using the correct special character format
   - Try using @...@ to isolate the problematic word

2. **HTML not rendering properly**
   - Make sure you selected an HTML output format
   - Check if your browser supports Ruby annotations
   - Download the result and open in a modern browser

3. **File upload errors**
   - Ensure your text files are UTF-8 encoded
   - Check that CSV files have the correct format (two columns)
   - Make sure JSON files follow the required structure

4. **Performance issues with large texts**
   - Enable parallel processing in Advanced Settings
   - Process text in smaller segments
   - Use a more powerful device if possible

### Getting Help

For more information or to report issues, visit the GitHub repository:
https://github.com/TakafumiYamauchi/Esperanto-Kanji-Converter-and-Ruby-Annotation-Tool-English