# Contributing to Documentation

Thank you for your interest in improving our documentation! This guide will help you contribute effectively.

## 📋 Table of Contents

- [Getting Started](#getting-started)
- [Documentation Standards](#documentation-standards)
- [Writing Guidelines](#writing-guidelines)
- [Code Examples](#code-examples)
- [Review Process](#review-process)
- [Tools and Setup](#tools-and-setup)

## 🚀 Getting Started

### Prerequisites

- Git knowledge
- Markdown experience
- Understanding of the codebase
- Node.js (for running documentation tools)

### Setting Up

1. **Fork the repository**
   ```bash
   git clone https://github.com/your-org/repository.git
   cd repository
   ```

2. **Install dependencies**
   ```bash
   npm install
   ```

3. **Start documentation server**
   ```bash
   npm run docs:dev
   ```

4. **Create a feature branch**
   ```bash
   git checkout -b docs/your-feature-name
   ```

## 📝 Documentation Standards

### File Structure

```
docs/
├── README.md              # Main documentation index
├── api/                   # API documentation
│   ├── README.md
│   ├── endpoints/
│   └── examples/
├── functions/             # Function documentation
│   ├── README.md
│   └── categories/
├── components/            # Component documentation
│   ├── README.md
│   └── component-guides/
├── examples/              # Practical examples
│   ├── README.md
│   └── tutorials/
└── guides/               # In-depth guides
    ├── README.md
    └── topics/
```

### Naming Conventions

- **Files**: Use kebab-case (`component-name.md`)
- **Directories**: Use kebab-case (`api-reference/`)
- **Anchors**: Use kebab-case (`#getting-started`)
- **Images**: Use kebab-case (`component-example.png`)

### Required Sections

Every documentation page must include:

1. **Title** (H1)
2. **Brief description**
3. **Table of contents** (for longer pages)
4. **Main content** with examples
5. **Related links** (if applicable)

## ✍️ Writing Guidelines

### Tone and Style

- **Clear and concise**: Use simple, direct language
- **Conversational**: Write as if explaining to a colleague
- **Inclusive**: Use inclusive language and examples
- **Action-oriented**: Start with verbs when possible

### Structure

#### Headers
```markdown
# Page Title (H1)
## Major Section (H2)
### Subsection (H3)
#### Detail (H4)
```

#### Lists
- Use bullet points for unordered lists
- Use numbers for step-by-step instructions
- Keep list items parallel in structure

#### Links
```markdown
- [Internal link](./relative-path.md)
- [External link](https://example.com)
- [Anchor link](#section-name)
```

### Content Organization

#### API Documentation
```markdown
### `functionName(parameters)`

Brief description of what the function does.

**Parameters:**
- `param1` (type): Description
- `param2` (type, optional): Description

**Returns:** `type` - Description

**Throws:** `ErrorType` - When this error occurs

**Example:**
```javascript
// Usage example
const result = functionName(arg1, arg2);
```

**See also:** [Related function](./link.md)
```

#### Component Documentation
```markdown
### ComponentName

Brief description of the component.

**Props:**
```typescript
interface ComponentProps {
  prop1: string;           // Required prop description
  prop2?: number;          // Optional prop description
  prop3: 'a' | 'b' | 'c';  // Union type description
}
```

**Examples:**
```jsx
// Basic usage
<ComponentName prop1="value" />

// Advanced usage
<ComponentName 
  prop1="value"
  prop2={42}
  prop3="a"
>
  Content
</ComponentName>
```

**Accessibility:** WCAG compliance notes

**See also:** [Related component](./link.md)
```

## 💻 Code Examples

### Best Practices

1. **Complete examples**: Show working, copy-pastable code
2. **Multiple scenarios**: Basic, intermediate, and advanced usage
3. **Error handling**: Include error handling patterns
4. **Comments**: Explain non-obvious code
5. **Imports**: Show all necessary imports

### Code Block Guidelines

#### Language Tags
```markdown
```javascript
// JavaScript code
```

```typescript
// TypeScript code
```

```jsx
// React JSX code
```

```bash
# Shell commands
```

```json
{
  "config": "value"
}
```
```

#### Example Structure
```markdown
**Basic Example:**
```javascript
// Simple usage
import { functionName } from 'package';

const result = functionName('input');
console.log(result); // Expected output
```

**Advanced Example:**
```javascript
// Complex scenario with error handling
import { functionName, ErrorType } from 'package';

try {
  const result = functionName('input', {
    option1: true,
    option2: 'value'
  });
  
  // Process result
  console.log('Success:', result);
} catch (error) {
  if (error instanceof ErrorType) {
    console.error('Expected error:', error.message);
  } else {
    console.error('Unexpected error:', error);
  }
}
```

**React Example:**
```jsx
import React, { useState } from 'react';
import { Component } from 'component-library';

function ExampleApp() {
  const [value, setValue] = useState('');

  return (
    <Component
      value={value}
      onChange={(newValue) => setValue(newValue)}
      placeholder="Enter text"
    />
  );
}
```
```

### Testing Examples

All code examples should be:
- **Tested**: Verify examples work as shown
- **Linted**: Follow code style guidelines
- **Updated**: Keep examples current with API changes

## 🔍 Review Process

### Before Submitting

1. **Spell check**: Use a spell checker
2. **Link check**: Verify all links work
3. **Code test**: Test all code examples
4. **Preview**: Review rendered output
5. **Accessibility**: Check for accessibility compliance

### Pull Request Guidelines

#### Title Format
```
docs: add component documentation for Button

docs: update API examples for user endpoints
docs: fix typos in function documentation
docs: improve accessibility guidelines
```

#### Description Template
```markdown
## Description
Brief description of changes

## Type of change
- [ ] New documentation
- [ ] Update existing documentation
- [ ] Fix typos/errors
- [ ] Improve examples
- [ ] Other (please describe)

## Checklist
- [ ] Spell checked
- [ ] Links verified
- [ ] Code examples tested
- [ ] Follows style guide
- [ ] Includes examples where appropriate
- [ ] Updates table of contents if needed

## Related Issues
Closes #123
Related to #456
```

### Review Criteria

Reviewers will check for:
- **Accuracy**: Technical correctness
- **Clarity**: Easy to understand
- **Completeness**: All necessary information included
- **Consistency**: Follows established patterns
- **Examples**: Working, relevant examples
- **Links**: All links functional

## 🛠️ Tools and Setup

### Required Tools

1. **Markdown editor** (VS Code recommended)
2. **Spell checker** (Code Spell Checker extension)
3. **Markdown linter** (markdownlint extension)
4. **Git** for version control

### VS Code Extensions

```json
{
  "recommendations": [
    "davidanson.vscode-markdownlint",
    "streetsidesoftware.code-spell-checker",
    "yzhang.markdown-all-in-one",
    "shd101wyy.markdown-preview-enhanced"
  ]
}
```

### Local Development

#### Documentation Server
```bash
# Start development server
npm run docs:dev

# Build documentation
npm run docs:build

# Lint documentation
npm run docs:lint

# Test examples
npm run docs:test-examples
```

#### Validation Scripts
```bash
# Check all links
npm run docs:check-links

# Spell check
npm run docs:spell-check

# Validate code examples
npm run docs:validate-examples
```

### Automation

#### Pre-commit Hooks
```bash
# Install pre-commit hooks
npm run prepare

# Hooks will run:
# - Spell check
# - Markdown lint
# - Link validation
# - Code example testing
```

#### CI/CD

Our continuous integration will:
- Build documentation
- Test all code examples
- Check links
- Validate markdown
- Deploy to staging for review

## 📊 Documentation Metrics

We track:
- **Page views**: Most accessed pages
- **User feedback**: Ratings and comments
- **Search queries**: What users look for
- **Bounce rate**: Page effectiveness

### Contributing to Metrics

Help improve documentation by:
- Adding user feedback widgets
- Improving search functionality
- Creating missing content
- Updating outdated information

## 🎯 Content Guidelines

### Writing for Different Audiences

#### Beginners
- Start with basics
- Explain terminology
- Provide context
- Use simple examples
- Link to prerequisites

#### Intermediate Users
- Focus on practical applications
- Show best practices
- Include performance tips
- Demonstrate integration patterns

#### Advanced Users
- Provide comprehensive reference
- Show edge cases
- Include architecture details
- Document extension points

### Accessibility in Documentation

1. **Alt text**: Describe all images
2. **Headers**: Use semantic heading structure
3. **Links**: Use descriptive link text
4. **Tables**: Include headers and captions
5. **Code**: Provide text alternatives for visual code

Example:
```markdown
![Button component with primary variant](./images/button-primary.png "A blue button with white text reading 'Save Changes'")
```

### Inclusive Language

- Use "they/them" for generic references
- Avoid assumptions about user background
- Include diverse examples and names
- Use clear, jargon-free language
- Provide definitions for technical terms

## 🚀 Getting Help

### Resources

- [Markdown Guide](https://www.markdownguide.org/)
- [Writing Style Guide](./style-guide.md)
- [API Documentation Standards](./api-standards.md)
- [Component Documentation Templates](./templates/)

### Support Channels

- **GitHub Issues**: For bugs or feature requests
- **Discord #docs**: For quick questions
- **Email**: docs@your-domain.com for complex issues
- **Office Hours**: Tuesdays 2-3 PM EST

### Common Questions

**Q: How do I add a new API endpoint?**
A: Follow the [API documentation template](./templates/api-endpoint.md)

**Q: Should I include TypeScript definitions?**
A: Yes, always include TypeScript interfaces when available

**Q: How do I test code examples?**
A: Run `npm run docs:test-examples` to validate all examples

**Q: What if I find outdated information?**
A: Create an issue or submit a PR with corrections

## 🏆 Recognition

Contributors who improve documentation significantly will be:
- Featured in release notes
- Added to contributors list
- Invited to documentation team
- Recognized in community channels

Thank you for helping make our documentation better! 🙏

---

**Questions?** Reach out to the documentation team or create an issue.