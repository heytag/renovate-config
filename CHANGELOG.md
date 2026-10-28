# Changelog

## [1.3.0](https://github.com/heytag/renovate-config/compare/renovate-config-v1.2.0...renovate-config-v1.3.0) (2026-10-28)


### Features

* add customManagers regex preset for workflow version pinning ([347f4b3](https://github.com/heytag/renovate-config/commit/347f4b3bcb922a0d5a53bb21c255af5a212f2e98))
* add internalChecksFilter none, fix trivy-action lookup, enable v-tag prefix ([3cd4d2e](https://github.com/heytag/renovate-config/commit/3cd4d2e8323507019d2c3de90b5327acd91bedc0))
* add internalChecksFilter none, fix trivy-action lookup, enable v-tag prefix ([702d94a](https://github.com/heytag/renovate-config/commit/702d94a76aa36112316df7201a9deef9c800d382))
* **automerge:** extend automerge to bun and docker-compose managers ([5c2f75a](https://github.com/heytag/renovate-config/commit/5c2f75a8422eb4a0f893a27020f994dbf14501e6))
* delay automerge by 3 days via minimumReleaseAge ([6b8126b](https://github.com/heytag/renovate-config/commit/6b8126b900493b4cf51e9eaa8e08c0b46a991c94))
* delay automerge by 3 days via minimumReleaseAge ([5ab3e67](https://github.com/heytag/renovate-config/commit/5ab3e678b2f280e8f82610235c7e9a0c40f8b7ad))
* initial Renovate runner and shared config preset ([1fec967](https://github.com/heytag/renovate-config/commit/1fec967a10b4cd4ae39d726b6196cd403e5eac99))
* **renovate:** add versioning, semantic commits, dashboard, and sGTM schedule ([46fbc44](https://github.com/heytag/renovate-config/commit/46fbc44903391a4d6d95c11c0b6c46185edae4bf))
* **renovate:** add versioning, semantic commits, dashboard, and sGTM schedule ([9f832f2](https://github.com/heytag/renovate-config/commit/9f832f27d6352a69617058ce60e72922af407b73))
* **renovate:** enable gitSubmodules manager for claude-conventions sync ([a7efe89](https://github.com/heytag/renovate-config/commit/a7efe89e378a07fc7a3a30396f772c6f8fe4a371))
* **renovate:** enable gitSubmodules manager for claude-conventions sync ([36ddbaf](https://github.com/heytag/renovate-config/commit/36ddbaff28b84ebebdc7dfd253f96c8ae289e215))
* run daily with catch-all patch automerge ([d83f265](https://github.com/heytag/renovate-config/commit/d83f265c0d81d39d4bcb4cbe2bb63fffe28b6f02))
* run daily with catch-all patch automerge ([cc91af6](https://github.com/heytag/renovate-config/commit/cc91af6d5de32efb39e629ba965cce38dd709b4b))
* track version pins in action.yml default values ([eb1d413](https://github.com/heytag/renovate-config/commit/eb1d4133b0d1cce78e391257132cb899913de1e6))
* track version pins in action.yml default values ([531cf27](https://github.com/heytag/renovate-config/commit/531cf27ef20377441fdb5052b2a5bb558b9d3797))


### Bug Fixes

* automerge only via Renovate after green checks ([5d7e04c](https://github.com/heytag/renovate-config/commit/5d7e04cee590e74de187cc2ef9f0047e3b9c06ba))
* automerge only via Renovate after green checks ([a1834a2](https://github.com/heytag/renovate-config/commit/a1834a272e78333999104ef6a961c2b9396e928c))
* **ci:** fix deprecated matchPackagePatterns, version mismatch, checkout@v4→v7 ([be1ead7](https://github.com/heytag/renovate-config/commit/be1ead7af1c95329efc1daad644631e42288d15e))
* **ci:** stop creating a duplicate v-prefixed tag on release ([8d5aa59](https://github.com/heytag/renovate-config/commit/8d5aa59cdbb8e442bebb75473f3e22b7e34ba600))
* **ci:** stop creating a duplicate v-prefixed tag on release ([d731ca0](https://github.com/heytag/renovate-config/commit/d731ca07bdc63fdbbede18c02161e2f6e3a61641))
* **ci:** trigger validate on release-please branch pushes ([#20](https://github.com/heytag/renovate-config/issues/20)) ([7ddb913](https://github.com/heytag/renovate-config/commit/7ddb9134035e62d2c523dcc079b08d612e3bdd51))
* **ci:** update validate paths after moving configs to .github/ ([3ea7545](https://github.com/heytag/renovate-config/commit/3ea75452cc659dcd04ea2691eeefff0ea22afb31))
* **ci:** use --package renovate flag to resolve renovate-config-validator binary ([7b5e015](https://github.com/heytag/renovate-config/commit/7b5e01508d69fad625d502f26daf5329ba298dc1))
* **config:** remove deprecated gitSubmodules option (removed in Renovate v38+) ([0b20445](https://github.com/heytag/renovate-config/commit/0b2044509d7eb745a82b2b7222a99dffa0f99afd))
* **docker:** ignore internal STACKIT base images, bump to 1.0.1 ([481b893](https://github.com/heytag/renovate-config/commit/481b893eec6876a8de9e94c0f0a89bcb4c05bda7))
