# OpenChatML v0.1 Specification

## 1. Introduction
OpenChatML is a markup language designed for representing conversational data in a structured format. It provides a standardized way to encode chat messages, including the role of the speaker, the content of the message, and optional metadata such as the name of the speaker.

## 2. Tokens
OpenChatML uses the following special tokens:

- `<s>`: Beginning of Sequence (BOS) token, indicating the start of a conversation.
- `</s>`: End of Sequence (EOS) token, indicating the end of the conversation.
- `<|im_start|>`: Start of Turn token, indicating the beginning of a new message within the conversation. 
- `<|im_end|>`: End of Turn token, indicating the end of the current message.
- `<|fim_prefix|>`: Before Cursor token, indicating the content before the cursor in a fill-in-the-middle task.
- `<|fim_suffix|>`: After Cursor token, indicating the content after the cursor in a fill-in-the-middle task.
- `<|fim_middle|>`: At Cursor token, indicating where the model should fill in content in a fill-in-the-middle task.
- `<|file_separator|>`: File Separator token, used to separate content from different files within the same sequence.

## 3. Message Structure
Each message in OpenChatML is represented as follows:

```
<|im_start|>role [name=<name>]
message_content
<|im_end|>
```

- `role`: A string indicating the role of the speaker. It must be one of the following: "system", "tool", "user", or "assistant".
- `name` (optional): A string representing the name of the speaker. If present, it should be added after the role, in the format `name=<name>`. The name cannot contain whitespace.
- `message_content`: The actual content of the message, which can span multiple lines.

## 4. Conversation Structure
A conversation in OpenChatML is represented as a sequence of messages, enclosed within `<s>` and `</s>` tokens:

```
<s>
<|im_start|>role1 [name=<name1>]
message1
<|im_end|>
<|im_start|>role2 [name=<name2>]
message2
<|im_end|>
...
</s>
```

## 5. Fill-in-the-Middle Tasks
OpenChatML supports fill-in-the-middle (FIM) tasks where the model is asked to complete content given surrounding context. The FIM structure is represented as:

```
<|fim_prefix|>prefix_content<|fim_middle|><|fim_suffix|>suffix_content
```

The model should generate content to replace the `<|fim_middle|>` token, using `prefix_content` as the preceding context and `suffix_content` as the following context. The generated content should smoothly connect the prefix to the suffix.

## 6. Multi-File Sequences
OpenChatML allows combining content from multiple files into a single sequence using the `<|file_separator|>` token:

```
file1_content
<|file_separator|>
file2_content
<|file_separator|>
file3_content
```

The `<|file_separator|>` token is used to demarcate the boundaries between content from different files while keeping them as part of the same overall sequence. This can be useful for tasks involving multiple input sources.

## 7. Examples
Here are a few examples of OpenChatML structures:

Example conversation:
```
<s>
<|im_start|>user
Hello there, AI.
<|im_end|>
<|im_start|>assistant
Hi. Nice to meet you.
<|im_end|>
</s>
```

Example conversation with speaker name:
```
<s>
<|im_start|>user name=Eric
Hello there, AI.
<|im_end|>
<|im_start|>assistant
Hi Eric. Nice to meet you.
<|im_end|>
</s>
```

Example fill-in-the-middle task:
```
<|fim_prefix|>The capital of France is <|fim_middle|><|fim_suffix|>, which is known for its famous Eiffel Tower.
```

Example multi-file sequence:
```
This is the content from the first file.
<|file_separator|>
This is the content from the second file.
And this is more content from the second file.
<|file_separator|>
Finally, this is the content from the third file.
```

## 8. Parsing and Generation
When parsing OpenChatML, the following rules should be applied:
- The `<s>`, `</s>`, `<|im_start|>`, `<|im_end|>`, `<|fim_prefix|>`, `<|fim_middle|>`, `<|fim_suffix|>`, and `<|file_separator|>` tokens are treated as special tokens and should not be considered part of the message content.
- The `role` must be one of the predefined values: "system", "tool", "user", or "assistant".
- The `name` attribute is optional and should be parsed if present.

When generating OpenChatML, the same structure and rules should be followed to ensure compatibility and consistency.
