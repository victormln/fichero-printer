# Fichero Printer - Project Context

## Overview
This repository contains a full set of tools for interacting with the **Fichero D11s** thermal label printer (also known as AiYin D11s). It includes a reverse-engineered protocol, a Python CLI/Library, and a modern Web GUI.

## Project Structure
- `fichero/`: The core Python library and CLI implementation.
- `web/`: A Svelte 5 + Fabric.js web application for designing and printing labels via Web Bluetooth.
- `docs/`: Technical documentation, including the detailed `PROTOCOL.md`.
- `tests/`: Unit tests for the Python library.

## Tech Stack
- **Python Backend**: Python 3.10+, `bleak` for BLE, `pillow` for image processing, `numpy` for data handling. Managed with `uv`.
- **Web Frontend**: Svelte 5, Fabric.js (canvas manipulation), Lucide icons.
- **Protocol**: Custom BLE byte-stream protocol reverse-engineered from the original Android app.

## Development Workflow
- **CLI**: Use `uv run fichero` for testing commands.
- **Web**: Standard Svelte development flow.
- **Documentation**: All protocol changes must be documented in `docs/PROTOCOL.md`.

## Key Goals
- Provide a privacy-respecting alternative to the official app.
- Offer a powerful web-based label designer.
- Enable automation and scripting via a clean Python API.
