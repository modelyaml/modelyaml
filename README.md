# model.yaml - Open Standard for AI Models

## An open standard for defining crossplatform, composable AI models

---

### What is model.yaml?

AI models are often available in multiple formats and variants, while different machines support diverse engines such as `llama.cpp` and `MLX`. This diversity can make it challenging to manage and deploy models effectively.

`model.yaml` addresses this challenge by providing a unified configuration format that defines all possible sources and formats of a model. It delegates the responsibility to the runtime to determine the most suitable variant to download and the appropriate engine to use, ensuring seamless compatibility and efficient deployment.

#### Key Features:

- **Open Standard**
- **Cross-platform**
- **Composable**
- **First Draft**

```yaml
model: org/model-name
base:
  - key: user/model-file
    sources:
      - type: huggingface
        user: huggingface-user
        repo: model-repo
```

---

## Specification (Draft 1.0)

The `model.yaml` standard defines a structured way to specify AI models, their sources, configurations, and metadata. Feel free to contribute to this open standard as it evolves.

You can find a TypeScript definition of the specification in [lmstudio.js](https://github.com/lmstudio-ai/lmstudio-js/blob/main/packages/lms-shared-types/src/VirtualModelDefinition.ts).

### Core Fields

#### `model` (Required)

The identifier for the model in the format `organization/name`. This determines where the model will be published and how it's referenced.

```yaml
model: meta/qwen3-8b
```

#### `base` (Required)

Defines the underlying model(s) that this virtual model points to. Can be either:

- A string referencing another virtual model, forming a chain
- An array of concrete model specifications with download sources

```yaml
base:
  - key: lmstudio-community/qwen3-8b-gguf
    sources:
      - type: huggingface
        user: lmstudio-community
        repo: Qwen-3-8B-GGUF
```

### Configuration & Metadata

#### `metadataOverrides` (Optional)

Overrides metadata for the model, which can differ from the base model. This helps platforms understand the model's capabilities.

```yaml
metadataOverrides:
  domain: llm
  architectures:
    - llama
  compatibilityTypes:
    - gguf
    - safetensors
  paramsStrings:
    - 1B
  minMemoryUsageBytes: 1000000000
  contextLengths:
    - 131072
  trainedForToolUse: mixed
  vision: false
```

- **`domain`**: The domain type of the model (e.g., `llm`, `embedding`).
- **`architectures`**: Array of model architecture names (e.g., `llama`, `qwen2`).
- **`compatibilityTypes`**: Array of format types the model supports (e.g., `gguf`, `safetensors`).
- **`paramsStrings`**: Human-readable parameter size labels (e.g., `1B`, `7B`).
- **`minMemoryUsageBytes`**: Minimum RAM required to load the model in bytes.
- **`contextLengths`**: Array of supported context window sizes.
- **`trainedForToolUse`**: Whether the model supports tool use (`true`, `false`, or `mixed`).
- **`vision`**: Whether the model supports processing images (`true`, `false`, or `mixed`).

#### `config` (Optional)

Built-in configurations for the model, applying preset configurations for loading or runtime operation.

```yaml
config:
  operation:
    fields:
      - key: llm.prediction.topKSampling
        value: 20
      - key: llm.prediction.minPSampling
        value:
          checked: true
          value: 0
```

### Customization

#### `customFields` (Optional)

User-configurable options that affect the model's behavior. Each field can trigger effects like changing variables or modifying the system prompt.

```yaml
customFields:
  - key: enableThinking
    displayName: Enable Thinking
    description: Enable the model to think before answering.
    type: boolean
    defaultValue: true
    effects:
      - type: setJinjaVariable
        variable: enable_thinking
```

- **`key`**: Unique identifier for the field.
- **`displayName`**: Human-readable name shown in UI.
- **`description`**: Explains the field's purpose.
- **`type`**: Data type (`boolean` or `string`).
- **`defaultValue`**: Initial value.
- **`effects`**: What effects to apply.

#### `suggestions` (Optional)

Dynamic configuration recommendations based on conditions. These appear in the UI when conditions are met.

```yaml
suggestions:
  - message: The following parameters are recommended for thinking mode
    conditions:
      - type: equals
        key: $.enableThinking
        value: true
    fields:
      - key: llm.prediction.temperature
        value: 0.6
```

- **`message`**: Text shown to the user.
- **`conditions`**: When the suggestion should appear.
- **`fields`**: Configuration values to apply.

---

## Complete Example

```yaml
model: qwen/qwen3-8b
base:
  - key: lmstudio-community/qwen3-8b-gguf
    sources:
      - type: huggingface
        user: lmstudio-community
        repo: Qwen-3-8B-GGUF
metadataOverrides:
  domain: llm
  architectures:
    - llama
  compatibilityTypes:
    - gguf
    - safetensors
  paramsStrings:
    - 1B
  minMemoryUsageBytes: 1000000000
  contextLengths:
    - 131072
  trainedForToolUse: mixed
  vision: false
config:
  operation:
    fields:
      - key: llm.prediction.topKSampling
        value: 20
      - key: llm.prediction.minPSampling
        value:
          checked: true
          value: 0
customFields:
  - key: enableThinking
    displayName: Enable Thinking
    description: Enable the model to think before answering.
    type: boolean
    defaultValue: true
    effects:
      - type: setJinjaVariable
        variable: enable_thinking
suggestions:
  - message: The following parameters are recommended for thinking mode
    conditions:
      - type: equals
        key: $.enableThinking
        value: true
    fields:
      - key: llm.prediction.temperature
        value: 0.6
```

---

## Why Use model.yaml?

### Composability

Build model chains by referencing other models, creating a stack of configurations.

### Cross-Platform

Define once, run anywhere that supports the standard, regardless of hardware or software.

### Preconfigured

Models come with optimal default settings built in, improving out-of-the-box performance.

### Adaptable

Custom fields enable dynamic behavior changes without modifying the core model.

---

`model.yaml` is an open standard ・ First Draft ・ [Contribute on GitHub](https://github.com/modelyaml/modelyaml)
