# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Repository Overview

A JetBrains Writerside documentation site covering TDD, Refactoring, DDD, Spring, JPA, and related software engineering topics. Published to GitHub Pages at https://msbaek.github.io/codetemplate/default-topic.html.

## Architecture

- **Writerside/topics/**: All documentation content as Markdown files
- **Writerside/hi.tree**: Table of contents and navigation structure (XML). Defines page hierarchy — new topics must be registered here to appear in the site
- **Writerside/writerside.cfg**: Writerside project configuration pointing to topics, images, categories, and vars
- **Writerside/images/**: Image assets referenced by topics
- **Writerside/c.list**: Category definitions
- **Writerside/v.list**: Variable definitions
- **.github/workflows/deploy.yml**: GitHub Actions workflow that builds and deploys to GitHub Pages on push to `main`

## Deployment

Pushing to `main` triggers automatic build and deploy via GitHub Actions:

- Uses `JetBrains/writerside-github-action@v4` with Docker version `232.10275`
- Instance ID: `hi` (referenced as `Writerside/hi` in workflow)
- Deploys to GitHub Pages

## Adding Content

1. Create a new `.md` file in `Writerside/topics/`
2. Add a `<toc-element topic="YourFile.md"/>` entry in `Writerside/hi.tree` at the appropriate hierarchy level
3. Reference images from `Writerside/images/`

## Content Structure (hi.tree)

Top-level sections: Architectures, Java, Legacy Code, Refactoring, TDD, DDD, Spring, JPA, Tools, Books, Tidy First, Articles, Practices, React, Terms, References, Etc, Inbox
