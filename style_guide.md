# Sui Documentation Style Guide

Version 1.0 | Last updated: January 2026

## Purpose

This style guide ensures consistency, clarity, and accessibility across all Sui documentation. It covers writing style, formatting, terminology, and code examples.

## Core Principles

1. **User-first** - Write for your audience's needs, not your expertise level
2. **Action-oriented** - Help users accomplish tasks
3. **Consistent** - Follow established patterns
4. **Accessible** - Clear to non-native English speakers
5. **Accurate** - Technically correct and current

---

## Voice and Tone

### Voice

**Direct and professional** - Address users as "you," avoid passive voice

✅ **Good:** "You can deploy your contract to testnet."  
❌ **Avoid:** "The contract can be deployed to testnet by the developer."

**Confident but humble** - Be authoritative without being condescending

✅ **Good:** "This approach works well for most use cases."  
❌ **Avoid:** "Obviously, this is the best approach."

**Inclusive** - Write for global, diverse audiences

✅ **Good:** "Complete these steps to get started."  
❌ **Avoid:** "Simply complete these easy steps."

### Tone by Content Type

| Content Type | Tone | Example |
|--------------|------|---------|
| Quickstarts | Encouraging, energetic | "Let's build your first dApp!" |
| Concepts | Explanatory, patient | "Objects are fundamental to Sui..." |
| Tutorials | Step-by-step, supportive | "Next, you'll create a function..." |
| Reference | Precise, neutral | "`sui client publish` - Publishes a Move package" |
| Troubleshooting | Helpful, reassuring | "This error typically occurs when..." |

---

## Grammar and Mechanics

### Capitalization

**Sentence case for headings**

✅ **Good:** "Understanding Sui objects"  
❌ **Avoid:** "Understanding Sui Objects"

**Product names use proper capitalization**
- Sui (always capitalized)
- Move (always capitalized)
- Mysten Labs (two words, both capitalized)

### Punctuation

**Serial comma (Oxford comma)**

✅ **Good:** "Objects can be owned, shared, or immutable."  
❌ **Avoid:** "Objects can be owned, shared or immutable."

**One space after periods**

✅ **Good:** "Deploy your contract. Then test it."  
❌ **Avoid:** "Deploy your contract.  Then test it."

### Numbers

**Spell out one through nine, use numerals for 10+**

✅ **Good:** "You need three validators."  
✅ **Good:** "The network has 150 validators."

**Exception:** Technical values, code, measurements

✅ "Set `gas-budget` to 5000000"  
✅ "The object is 2 MB"

---

## Terminology

### Preferred Terms

| Use This | Not This | Notes |
|----------|----------|-------|
| smart contract | contract, dapp | Industry standard |
| developer | engineer, coder | Inclusive term |
| on-chain | on chain, onchain | Hyphenated adjective |
| testnet | test net, test network | One word |
| mainnet | main net, production | One word |
| command line | command-line (noun) | Hyphenate when adjective |
| setup (noun) | set-up | One word as noun |
| set up (verb) | setup | Two words as verb |

### Sui-Specific Terms

**Always capitalize:**
- Sui (the blockchain)
- Move (the language)
- SUI (the token)

**Lowercase:**
- object (unless starting a sentence)
- transaction
- module
- package

**Use consistently:**
- "Sui object" not "object on Sui"
- "Move module" not "module written in Move"

---

## Document Structure

### Every document should include:

1. **Title** - Clear, descriptive (sentence case)
2. **Metadata** - Audience, time, prerequisites
3. **Introduction** - What and why
4. **Body** - Organized, scannable sections
5. **Conclusion** - Summary and next steps
6. **Navigation** - Links to related content

### Example Structure:

```markdown
# Title of Document

**Audience:** Who this is for  
**Reading time:** Estimated duration  
**Prerequisites:** What users need first

## Introduction

Brief overview of what this document covers and why it matters.

## Main Content

### Section 1
Content with clear headings...

### Section 2
More content...

## Summary

Key takeaways from this document.

## Next Steps

- [Related Document 1](link)
- [Related Document 2](link)
```

---

## Formatting

### Headings

**Use meaningful, descriptive headings**

✅ **Good:** "## How to Deploy Smart Contracts"  
❌ **Avoid:** "## Deployment"

**Hierarchy:**
- H1 (#) - Document title only
- H2 (##) - Major sections
- H3 (###) - Subsections
- H4 (####) - Rarely needed

### Lists

**Use lists to break up dense information**

**Unordered lists** - For items without sequence:
```markdown
- First item
- Second item
- Third item
```

**Ordered lists** - For sequential steps:
```markdown
1. First step
2. Second step
3. Third step
```

**Use parallel structure** - Start all items similarly

✅ **Good:**
- Install the CLI
- Configure your environment
- Deploy your contract

❌ **Avoid:**
- Install the CLI
- Configuring your environment
- You should deploy your contract

### Emphasis

**Bold** - For UI elements, importance, first use of key terms

**Italic** - For emphasis (use sparingly)

**Code formatting** - For code, commands, file names

Examples:
- Click the **Submit** button
- This is *extremely* important (rare)
- Run `sui client publish`
- Edit the `Move.toml` file

---

## Code Examples

### General Principles

1. **Complete and runnable** - Users should be able to copy and run
2. **Commented appropriately** - Explain non-obvious code
3. **Follow best practices** - Demonstrate good patterns
4. **Formatted consistently** - Use standard indentation

### Code Block Format

````markdown
```language
// Your code here
```
````

**Specify the language:**

```move
module my_package::example {
    use sui::object::UID;
    
    public struct MyStruct has key {
        id: UID,
    }
}
```

```bash
sui client publish --gas-budget 100000000
```

### Command Line Examples

**Show the command and expected output:**

```bash
$ sui client gas

# Output:
╭────────────────────────────────────────────────────────╮
│ gas_id                    │ gas_value │ object_version │
├────────────────────────────────────────────────────────┤
│ 0x123...789               │ 100000000 │ 5              │
╰────────────────────────────────────────────────────────╯
```

**Use placeholders clearly:**

```bash
sui client call \
  --package <YOUR_PACKAGE_ID> \
  --module example \
  --function create
```

### Code Comments

**Comment purpose, not mechanics:**

✅ **Good:**
```move
// Verify the caller owns this capability
assert!(cap.owner == sender, ENotAuthorized);
```

❌ **Avoid:**
```move
// Check if cap.owner equals sender
assert!(cap.owner == sender, ENotAuthorized);
```

---

## Links and References

### Internal Links

**Use relative paths:**

```markdown
See [Understanding Objects](../concepts/sui-objects.md)
```

**Use descriptive link text:**

✅ **Good:** "Learn more about [object ownership](concepts/ownership.md)"  
❌ **Avoid:** "Click [here](concepts/ownership.md) for object ownership"

### External Links

**Open in same tab** - Don't force new tabs

**Provide context:**

✅ **Good:** "Refer to the [official Move documentation](https://move-lang.org) for details"  
❌ **Avoid:** "[Move docs](https://move-lang.org)"

---

## Accessibility

### Alt Text for Images

Always include descriptive alt text:

```markdown
![Diagram showing Sui transaction flow from user to validator](images/tx-flow.png)
```

### Diagrams

- Use high contrast colors
- Include text descriptions
- Ensure diagrams are referenced in body text

### Tables

- Use table headers
- Keep tables simple (max 5 columns)
- Provide table captions when helpful

---

## Common Mistakes to Avoid

❌ **Avoid:**
- Future tense ("you will create") → Use present ("you create")
- Latin abbreviations (e.g., i.e.) → Use "for example" or "that is"
- Gerunds in headings ("Creating Objects") → Use imperatives ("Create Objects")
- Exclamation marks (except in celebrations)
- Vague language ("might," "could," "possibly")

---

## Review Checklist

Before publishing documentation:

- [ ] Title is clear and descriptive
- [ ] Metadata (audience, time, prereqs) is included
- [ ] Introduction explains what and why
- [ ] Code examples are complete and tested
- [ ] Links work and use relative paths
- [ ] Grammar is correct (no typos)
- [ ] Terminology is consistent
- [ ] Navigation to related content is provided
- [ ] Document follows this style guide

---

## Questions or Suggestions?

This style guide evolves based on team feedback and user needs. To propose changes:

1. Open an issue describing the proposed change
2. Explain the rationale
3. Provide examples

---

**Document Owner:** Technical Writing Team  
**Last Review:** January 2026  
**Next Review:** April 2026