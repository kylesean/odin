# Installation and Configuration

> This chapter will guide you through the installation and configuration process of the Odin framework.

## System Requirements

Before installing Odin, please ensure your system meets the following requirements:

### Required Components
- PHP >= 8.0
- PHP Extensions:
  - bcmath
  - curl
  - mbstring
- Composer >= 2.0
- Hyperf Framework (2.2.x, 3.0.x or 3.1.x)

### Recommended Configuration
- PHP 8.1 or higher
- Swow/Swoole extension (for asynchronous processing)

## Installation Steps

### 1. Install via Composer

Install the Odin package in your Hyperf project using Composer:

```bash
composer require hyperf/odin
```

### 2. Publish Configuration Files

After installation, you need to publish Odin's configuration files to your project:

```bash
php bin/hyperf.php vendor:publish hyperf/odin
```

This will create an `odin.php` configuration file in your project's `config/autoload` directory.

### 3. Install Dependencies

Odin's core dependencies are already included in the package, including:

```bash
# Vector storage support
hyperf/qdrant-client

# For calculating token counts
yethee/tiktoken
```

If you need to use AWS Bedrock services, you need to install the AWS SDK additionally:

```bash
composer require aws/aws-sdk-php
```

## Initial Configuration Guide

### Basic Configuration

After installation, you first need to configure the LLM provider information you will use. Odin supports multiple LLM providers, including OpenAI, Azure OpenAI, AWS Bedrock, Doubao, ChatGLM, and other models.

### Configure Default Model

Set the default model in `config/autoload/odin.php`:

```php
return [
    'llm' => [
        'default' => 'gpt-4o-global', // Set your default model
        // ... other configurations
    ],
    // ... other configurations
];
```

## Environment Variable Configuration

Odin uses environment variables to manage sensitive information such as API keys. You need to configure these variables in the `.env` file in your project root directory.

### Common Environment Variables

```dotenv
# OpenAI Configuration
OPENAI_API_KEY=your_openai_api_key
OPENAI_BASE_URL=https://api.openai.com/v1  # Optional, defaults to OpenAI official API
OPENAI_ORG_ID=your_organization_id         # Optional, organization ID

# Azure OpenAI Configuration
AZURE_OPENAI_API_KEY=your_azure_openai_api_key
AZURE_OPENAI_API_BASE=https://your-resource-name.openai.azure.com
AZURE_OPENAI_API_VERSION=2023-05-15
AZURE_OPENAI_DEPLOYMENT_NAME=your_deployment_name

# GPT-4o Configuration Example (using Azure OpenAI)
AZURE_OPENAI_4O_API_KEY=your_azure_openai_4o_api_key
AZURE_OPENAI_4O_API_BASE=https://your-resource-name.openai.azure.com
AZURE_OPENAI_4O_API_VERSION=2023-07-01-preview
AZURE_OPENAI_4O_DEPLOYMENT_NAME=gpt-4o

# AWS Bedrock Configuration
AWS_ACCESS_KEY_ID=your_aws_access_key_id
AWS_SECRET_ACCESS_KEY=your_aws_secret_access_key
AWS_REGION=us-east-1

# ChatGLM Configuration
GLM_MODEL=THUDM/glm-edge-1.5b-chat

# Doubao Model Configuration
DOUBAO_API_KEY=your_doubao_api_key
DOUBAO_BASE_URL=https://api.doubao.com
```

## Configuration File Details

Odin's main configuration file is located at `config/autoload/odin.php`. Here's an explanation of the main configuration items:

### LLM Model Configuration

```php
'llm' => [
    'default' => 'gpt-4o-global', // Default model to use
    'general_model_options' => [
        // General model options
        'chat' => true,               // Whether to support chat functionality
        'function_call' => false,     // Whether to support function calling
        'embedding' => false,         // Whether to support embedding
        'multi_modal' => false,       // Whether to support multi-modal input
        'vector_size' => 0,           // Vector size
    ],
    'general_api_options' => [
        // General API options
        'timeout' => [
            'connection' => 5.0,      // Connection timeout (seconds)
            'write' => 10.0,          // Write timeout (seconds)
            'read' => 300.0,          // Read timeout (seconds)
            'total' => 350.0,         // Total timeout (seconds)
            'thinking' => 120.0,      // Thinking timeout (seconds)
            'stream_chunk' => 30.0,   // Stream chunk timeout (seconds)
            'stream_first' => 60.0,   // First stream chunk timeout (seconds)
        ],
        'custom_error_mapping_rules' => [], // Custom error mapping rules
    ],
    'models' => [
        // OpenAI Model Configuration Example
        'gpt-3.5-turbo' => [
            'implementation' => OpenAIModel::class,
            'config' => [
                'api_key' => env('OPENAI_API_KEY'),
                'base_url' => env('OPENAI_BASE_URL', 'https://api.openai.com/v1'),
                'organization' => env('OPENAI_ORG_ID', ''),
            ],
            'model_options' => [
                'name' => 'gpt-3.5-turbo',
                'chat' => true,
                'function_call' => true,
                'embedding' => false,
                'multi_modal' => false,
                'vector_size' => 0,
            ],
            'api_options' => [
                'timeout' => [
                    'connection' => 5.0,
                    'write' => 10.0,
                    'read' => 120.0,
                    'total' => 150.0,
                    'thinking' => 60.0,
                    'stream_chunk' => 10.0,
                    'stream_first' => 30.0,
                ],
                'custom_error_mapping_rules' => [],
            ],
        ],
        
        // Azure OpenAI Model Configuration Example
        'azure-gpt-4' => [
            'implementation' => AzureOpenAIModel::class,
            'config' => [
                'api_key' => env('AZURE_OPENAI_API_KEY'),
                'api_base' => env('AZURE_OPENAI_API_BASE'),
                'api_version' => env('AZURE_OPENAI_API_VERSION'),
                'deployment_name' => env('AZURE_OPENAI_DEPLOYMENT_NAME'),
            ],
            'model_options' => [
                'chat' => true,
                'function_call' => true,
                'embedding' => false,
                'multi_modal' => false,
                'vector_size' => 0,
            ],
            'api_options' => [
                'timeout' => [
                    'connection' => 5.0,
                    'write' => 10.0,
                    'read' => 300.0,
                    'total' => 350.0,
                    'thinking' => 120.0,
                    'stream_chunk' => 30.0,
                    'stream_first' => 60.0,
                ],
                'custom_error_mapping_rules' => [],
            ],
        ],
        
        // AWS Bedrock Model Configuration Example
        'claude-3-sonnet' => [
            'implementation' => AwsBedrockModel::class,
            'config' => [
                'key' => env('AWS_ACCESS_KEY_ID'),
                'secret' => env('AWS_SECRET_ACCESS_KEY'),
                'region' => env('AWS_REGION', 'us-east-1'),
                'model_id' => 'anthropic.claude-3-sonnet-20240229-v1:0',
            ],
            'model_options' => [
                'chat' => true,
                'function_call' => true,
                'embedding' => false,
                'multi_modal' => true,
                'vector_size' => 0,
            ],
            'api_options' => [
                'timeout' => [
                    'connection' => 5.0,
                    'write' => 10.0,
                    'read' => 300.0,
                    'total' => 350.0,
                    'thinking' => 120.0,
                    'stream_chunk' => 30.0,
                    'stream_first' => 60.0,
                ],
            ],
        ],
    ],
],
```

## Installation Verification

After installation, you can verify that the installation was successful with the following code:

```php
<?php

declare(strict_types=1);

! defined('BASE_PATH') && define('BASE_PATH', dirname(__DIR__, 1));
require_once BASE_PATH . '/vendor/autoload.php';

use Hyperf\Context\ApplicationContext;
use Hyperf\Di\ClassLoader;
use Hyperf\Di\Container;
use Hyperf\Di\Definition\DefinitionSourceFactory;
use Hyperf\Odin\Logger;
use Hyperf\Odin\Message\AssistantMessage;
use Hyperf\Odin\Message\SystemMessage;
use Hyperf\Odin\Message\UserMessage;
use Hyperf\Odin\Model\OpenAIModel;

// Initialize container
ClassLoader::init();
$container = ApplicationContext::setContainer(new Container((new DefinitionSourceFactory())()));

// Create model instance
$model = new OpenAIModel(
    'gpt-3.5-turbo',  // Model name
    [
        'api_key' => getenv('OPENAI_API_KEY'),
        'base_url' => getenv('OPENAI_BASE_URL') ?: 'https://api.openai.com/v1',
    ],
    new Logger(),
);

// Build messages
$messages = [
    new SystemMessage('You are a helpful AI assistant.'),
    new UserMessage('Hello, this is a test message'),
];

// Send request
$response = $model->chat($messages);

// Output response
$message = $response->getFirstChoice()->getMessage();
if ($message instanceof AssistantMessage) {
    echo $message->getContent();
}
```

If everything is configured correctly, you should see the model's response.

### Streaming Response Example

If you need to use streaming responses, you can use the following code:

```php
// ... previous code is the same ...

// Send streaming request
$response = $model->chatStream($messages);

// Handle streaming response
foreach ($response->getStreamIterator() as $choice) {
    $message = $choice->getMessage();
    if ($message instanceof AssistantMessage) {
        echo $message->getContent();
    }
}
```

## Troubleshooting

If you encounter issues during installation or configuration, please check:

1. Whether the PHP version meets the requirements
2. Whether the required PHP extensions are installed
3. Whether environment variables and configuration files are set correctly
4. Whether the API key is valid
5. Check the log files (usually located in `runtime/logs/`)

## Next Steps

- Learn about [Core Concepts](./02-core-concepts.md)
- Explore [API Reference](./03-api-reference.md)
- Learn how to [configure different model providers](./04-model-providers.md)