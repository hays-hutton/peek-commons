# Hostile document

Plain text before the payloads.

<script>alert('xss-1')</script>

<img src="x" onerror="alert('xss-2')">

<iframe src="https://evil.example.com"></iframe>

<style>body { display: none; }</style>

[click me](javascript:alert('xss-3'))

<a href="javascript:alert('xss-4')">anchor</a>

<div onclick="alert('xss-5')">clickable div</div>

Plain text after the payloads. This line must still render.
