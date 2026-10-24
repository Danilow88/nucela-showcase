# Nucela - AI-powered support assistant (case study)

*The full source code is private. This page describes the project, the architecture and a sanitized code sample.*

## Overview

Nucela is a FastAPI backend with a lightweight web front end that brings Jira/Confluence lookups, Google Docs content and Gemini (Vertex AI) answers into a single assistant for technical support work. It also ships as a native macOS app with a DMG installer.

## What I built

**Multi-source retrieval:** one API that combines ticket and knowledge-base data (Jira, Confluence), documents (Google Docs) and LLM answers (Gemini on Vertex AI).

**Flexible authentication:** an ordered auth chain for Vertex AI (OAuth refresh token, then application default credentials, then service account).

**Desktop packaging:** a native macOS app (PyInstaller + pywebview) and a DMG build script for distribution.

**Secure access options:** local HTTPS with mkcert, and tunnel-based access for restricted networks, so browser features such as the microphone work on remote machines.

**Operations:** health check endpoint, Swagger docs, environment-based configuration and Kubernetes manifests.

## Architecture

```
Browser / macOS app (pywebview)
        |
   FastAPI backend  --->  Jira / Confluence APIs
        |           --->  Google Docs API
        |           --->  Vertex AI (Gemini)
   Settings from environment (no secrets in the code)
```

## Design decision: pluggable credentials

Different environments authenticate differently: a developer laptop, a shared machine and a server do not use the same credentials. The backend tries an OAuth refresh-token flow first and falls back to Application Default Credentials, so the same code runs everywhere without changes. All secrets come from environment settings and are never committed.

## Code sample (simplified)

```python
def get_credentials(settings):
    """Use an OAuth refresh-token flow when fully configured, otherwise return None."""
    if not (settings.refresh_token and settings.client_id and settings.client_secret):
        return None  # caller falls back to Application Default Credentials

    creds = Credentials(
        token=None,
        refresh_token=settings.refresh_token,
        token_uri=GOOGLE_TOKEN_URI,
        client_id=settings.client_id,
        client_secret=settings.client_secret,
        scopes=[CLOUD_PLATFORM_SCOPE],
    )
    creds.refresh(Request())
    return creds


def get_access_token(settings):
    """Return a bearer token from the OAuth flow or from Application Default Credentials."""
    creds = get_credentials(settings)
    if creds is not None:
        return creds.token
    try:
        adc, _ = google.auth.default(scopes=[CLOUD_PLATFORM_SCOPE])
        if not adc.valid:
            adc.refresh(Request())
        return adc.token
    except Exception:
        return None
```

## Stack

Python, FastAPI, Vertex AI (Gemini), Jira and Confluence APIs, Google Docs API, PyInstaller, pywebview, Cloudflare Tunnel, Kubernetes

## Author

Danilo Fukuyama - [LinkedIn](https://www.linkedin.com/in/danilo-fukuyama-11a991277/)
