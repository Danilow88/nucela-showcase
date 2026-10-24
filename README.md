# Nucela - AI-powered support assistant (case study)

*The source code is private. This page describes the project, the architecture and what I built.*

## Overview

Nucela is a FastAPI backend with a lightweight web front end that brings Jira/Confluence lookups, Google Docs content and Gemini (Vertex AI) answers into a single assistant for technical support work. It also ships as a native macOS app with a DMG installer.

## What I built

**Multi-source retrieval:** one API that combines ticket and knowledge-base data (Jira, Confluence), documents (Google Docs) and LLM answers (Gemini on Vertex AI).

**Flexible authentication:** an ordered auth chain for Vertex AI (OAuth refresh token, then application default credentials, then service account).

**Desktop packaging:** a native macOS app (PyInstaller + pywebview) and a DMG build script for distribution.

**Secure access options:** local HTTPS with mkcert, and tunnel-based access for restricted networks, so browser features such as the microphone work on remote machines.

**Operations:** health check endpoint, Swagger docs, environment-based configuration and Kubernetes manifests.

## Stack

Python, FastAPI, Vertex AI (Gemini), Jira and Confluence APIs, Google Docs API, PyInstaller, pywebview, Cloudflare Tunnel, Kubernetes

## Author

Danilo Fukuyama - [LinkedIn](https://www.linkedin.com/in/danilo-fukuyama-11a991277/)
