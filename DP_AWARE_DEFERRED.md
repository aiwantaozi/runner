# Deferred SGLang DP-aware gateway build

Status: **deferred**, 2026-10-10. This branch preserves an experimental packaging
change. It is not an instruction to build/publish a service image or update the
measured dependency catalog. Active recipes remain on their existing baseline.

Downstream gateway contracts have not shipped in the published packages reviewed
for this integration; lifecycle and rank behavior need further review before
release. Existing merged bug fixes are outside this archive commit.

## Preserved change

The CUDA recipe builds the gateway from
`f620d733d256c535b00d6e2f6b8e43c27fa81214`, which contains the upstream PD dispatch
fix, with `pack/cuda/patches/sglang_router/001_pd_rank_contract.patch`. This revision
is a checked snapshot, not the commit that originally introduced DP dispatch.
The patch makes discovered rank UUID deletion remove its physical group, aligns
both request-rank fields, and clears the P-side prefill-location hint while the
D side carries the selected P rank. The wheel is marked `0.3.2+gpustack.dp1` to
distinguish this local contract from the unpatched public version. SGLang engine
0.5.18 remains unchanged.

The patch is not required for basic endpoint-level PD. Group-wide removal is an
integration choice, not independent rank failure isolation. Saving and recovering
the POST parent ID is an alternative to the deletion patch that still needs a
complete GPUStack lifecycle implementation and validation.

Historical validation: 315 packaging tests and 3 Rust contract tests passed;
a locally compiled gateway passed mock P2/D4 and P1/D2 runs with 74 requests each.
No complete CUDA image was built or published, and real GPU KV transfer was not
validated. The dependency catalog was not changed.

## Resumption

Recheck released gateway versions/APIs, re-evaluate each patch, and replace the
source build with a released compatible wheel if the required contracts exist.
Rebase onto current main, run packaging/contract regressions, build the complete
image, collect measured dependencies through Pack when release is authorized,
and validate real GPU PD/rank behavior before declaring support.

Companion branches:

- [GPUStack](https://github.com/aiwantaozi/gpustack/tree/archive/dp-aware-20261010)
- [plans and step-by-step validation](https://github.com/aiwantaozi/plans/tree/archive/dp-aware-20261010/gpustack/pd-disaggregation/dp)

This snapshot starts at runner `4e5ba29098baf0b20bc4388180603e825bea1bdc` and is
not rebased for archiving. Inspect it in a separate worktree; cherry-pick only
its archive commit when resuming, preserving fixes already merged into main.
