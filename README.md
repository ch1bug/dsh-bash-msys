# dsh-bash-msys

DSH bundle: native MSYS2 (UCRT64) bash for the DSH shell seam.

Fork/generalization of `@deepseek-ai/dsh-bash-local`: makes the bash binary
explicitly configurable (defaulting to the MSYS2 bash at a configurable root)
and injects the MSYS2 environment (MSYSTEM=UCRT64, CHERE_INVOKING, PATH) for
one-shot calls, so the model's `bash` tool and the session terminal both run a
real MSYS2 environment on Windows — pacman, cygpath, `/c/` paths, the full
`/usr/bin` toolchain.

> Provenance: derived from `@deepseek-ai/dsh-bash-local` 0.1.7-rc.2
> (© DeepSeek AI, MIT). Modifications: configurable bash path, MSYS2
> environment layering, MSYS-aware engine resolution. Upstream semantics kept
> where possible; deviations documented in `docs/adr/`.

Status: scaffold. See `CONTEXT.md` and GitHub Issues for the plan.
