# What is an `AGENTS.md` file?

An `AGENTS.md` file contains instructions for AI coding agents that work in a repository. It can explain the project's purpose, writing or coding conventions, safety expectations, and how to validate changes. Think of it as a handoff note that helps an agent understand how to contribute.

## What it does

Tools that support `AGENTS.md` may read its instructions when working in the repository and use them to guide their actions. For example, this repository's [`AGENTS.md`](../../AGENTS.md) asks agents to keep explanations beginner-friendly, link and credit source material, identify the environment for command examples, and warn about commands that can affect data or system settings.

The file itself does not run commands or change the repository. It is guidance, not an enforcement mechanism: it cannot guarantee that an agent follows every instruction, prevent other contributors from making changes, or replace testing and review. Support and behavior can vary between tools, so check the documentation for the agent you use.

## How it differs from a README

A `README.md` is primarily for people exploring a project; it describes what the project is and where to start. An `AGENTS.md` provides additional instructions for agents making changes. The two files can complement each other: a README introduces the project, while `AGENTS.md` sets expectations for contributing to it.

## When it is useful

Add an `AGENTS.md` when project-specific context or instructions would help an agent make consistent, safe changes. Keep it focused and accurate, and update it when the project's structure or conventions change. Avoid treating it as a substitute for clear documentation, review, or automated checks.

For product-specific behavior, see [GitHub's documentation on repository custom instructions](https://docs.github.com/en/copilot/customizing-copilot/adding-repository-custom-instructions-for-github-copilot).
