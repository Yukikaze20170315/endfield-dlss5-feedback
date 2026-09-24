# Release sources and verification scope

RHI manages installation and version selection. Its [Renodx5AddonService](https://github.com/RankFTW/RHI/blob/85505fca3874e98a5392b0b0647dfc088e6cf055/RenoDXCommander/Services/Renodx5AddonService.cs) reads rhi-repo releases, while its RenoDX database service consumes rhi-repo database files. The reviewed RHI patch notes describe installer behavior; they are not a complete Generic binary changelog.

This report combines controlled 5.2.1 game observations with separately identified static release-asset observations. See [the Generic report](GENERIC_PROPOSAL.md) for the 7.0.0-rc1/rc8 control inventory. The newer binaries have not been deployed or tested in Endfield as part of this review.
