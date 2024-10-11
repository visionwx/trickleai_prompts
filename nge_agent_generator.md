Below are the available functions and inputs:
- For the input type, it is used to provide an input interface to the user, collecting information entered by the user.
- For the function type, it is a utility function that has certain specific functionalities.

### input and function ###
```
{
    "type": "input",
    "input": {
        "name": "text_input",
        "description": "Collect text input from user",
        "type": "string",
        "alias_name": "the name used for reference",
        "value": "the current value of this input field"
    }
},
{
    "type": "input",
    "input": {
        "name": "single_choice",
        "description": "Provide options for the user to select, can only select one option, Particularly suitable for creating various forms, such as surveys, quizzes, information collection, etc.",
        "type": "string",
        "title": "the title of the single choice, can be the label name, or the question content",
        "enum": ["option 1", "option 2", ...],
        "alias_name": "the name used for reference"
    }
},
{
    "type": "input",
    "input": {
        "name": "image_input",
        "description": "Collect image url from the user",
        "type": "string",
        "alias_name": "the name used for reference",
        "value": "the current value of this input field"
    }
},
{
    "type": "function",
    "function": {
        "name": "text_generation",
        "alias_name": "the name used for reference",
        "description": "Generate text content using large language model",
        "parameters": {
            "type": "object",
            "properties": {
                "instruction": {
                    "type": "string",
                    "description": "The instruction for the large language model to generate text content, use '{alias_name}' to reference the content from input_value or function_output",
                }
            },
            "required": ["instruction"],
        }
    }
},
{
    "type": "function",
    "function": {
        "name": "text_generation_with_web_search",
        "alias_name": "the name used for reference",
        "description": "Generate text content using large language model and based web-sourced information. The process of this function: 1. analyze the input instruction, convert to several query keywords. 2. use the generated query keywords to search websites from internet by google search api. 3. filter the websites that are related to the user question and read detail website content. 4. Compose final answer according to the user instruction and searched website contents ",
        "parameters": {
            "type": "object",
            "properties": {
                "instruction": {
                    "type": "string",
                    "description": "The instruction for the large language model to generate text content, use '{alias_name}' to reference the content from input_value or function_output",
                }
            },
            "required": ["instruction"],
        }
    }
},
{
    "type": "function",
    "function": {
        "name": "read_web_page",
        "alias_name": "the name used for reference",
        "description": "Read web page content from the provided web url",
        "parameters": {
            "type": "object",
            "properties": {
                "url": {
                    "type": "string",
                    "description": "The web url to read, use '{alias_name}' to reference the content from input_value or function_output",
                }
            },
            "required": ["url"],
        }
    }
},
{
    "type": "function",
    "function": {
        "name": "search_image",
        "alias_name": "the name used for reference",
        "description": "Search images by google search",
        "parameters": {
            "type": "object",
            "properties": {
                "query": {
                    "type": "string",
                    "description": "The query used to search for related images, use '{alias_name}' to reference the content from input_value or function_output",
                }
            },
            "required": ["query"],
        }
    }
},
{
    "type": "function",
    "function": {
        "name": "text_to_podcast",
        "alias_name": "the name used for reference",
        "description": "Convert text to podcast",
        "parameters": {
            "type": "object",
            "properties": {
                "content": {
                    "type": "string",
                    "description": "The content that will be used to convert to podcast audio, use '{alias_name}' to reference the content from input_value or function_output",
                }
            },
            "required": ["content"],
        }
    }
}
```

### By combining these inputs and functions, simple applications can be developed. For example, below is a translation application:
```json
[
    {
        "type": "input",
        "input": {
            "name": "text_input",
            "alias_name": "content_to_translate"
        }
    },
    {
        "type": "input",
        "input": {
            "name": "single_choice",
            "alias_name": "translate_to_language",
            "enum": ["English", "Chinese"]
        }
    },
    {
        "type": "function",
        "function": {
            "name": "text_generation",
            "alias_name": "translator",
            "parameters": {
                "instruction": "Translate the content: \n{content_to_translate} to {translate_to_language}"
            }
        }
    }
]
```

### Some Details About the Application:
- The combined application runs procedurally from start to finish.
- Functions of the same type can appear multiple times within a single application.
- The 'name' fields in input or function are immutable and cannot be changed, use the 'alias_name' field to define identified and distinguished name. For example below, the 'name' filed with value 'text_input' is immutable, you cannot change it to other value, but you can change 'alias_name' field value
```
{
    "type": "input",
    "input": {
        "name": "text_input",
        "alias_name": "content_to_translate"
    }
}
```


### Instruction:
Your task is to:

Step 1. Analyze the user's question and describe the type of application needed to solve the user's question.

Step 2. Based on the inputs and functions provided above, as well as the application description from the first step, generate a set of combinations of inputs and functions to address the user's question.

Note:
- Only output the list data of the combinations of inputs and functions, without any explanatory supplementary text.
- The resulting application can be a simple one with just a single function, or it can be a complex workflow application that combines multiple inputs and functions.


### User Question：
{inputs.question}
