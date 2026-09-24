# RHI and rhi-repo scope

RHI is the installer and manager. Its `Renodx5AddonService` queries `https://api.github.com/repos/RankFTW/rhi-repo/releases?per_page=100`, filters `renodx-dlss5-*` and `renodx-dlss-SF-*` tags, and stages the selected addon. It can therefore discover a release such as RenoDX DLSS5 7.0.0-rc8 without a matching RHI source commit.

RHI also reads `rhi-repo/database/RenoDXdb.json` and `RenoDXdb-unreal.json`. RHI patch notes describe installer, version selection, deployment, cleanup and caching behavior. They are not a changelog for the internal implementation of a RenoDX DLSS5 binary.

Generic feature claims in this package rely on the release assets and static comparison of the Generic addon itself. RHI metadata can establish when the installer exposed or consumed a release, but cannot establish which hidden model parameters a binary consumes at runtime.

References checked on 2026-09-24:

- https://github.com/RankFTW/RHI
- https://github.com/RankFTW/rhi-repo/releases
- https://github.com/RankFTW/rhi-repo/releases/tag/renodx-dlss5-7.0.0-rc8
