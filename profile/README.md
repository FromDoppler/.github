# Doppler Projects

## Minimal requirements for new projects

- Only choose private repositories when the code has business value differentiator logic.

- Use [FromDoppler Organization](https://github.com/FromDoppler/) for public repositories and [MakingSense](https://github.com/MakingSense) for private ones.

- Do not store secrets in the repository

  If you really need to store secrets, store them encrypted using SOPS ([see more information](https://github.com/MakingSense/doppler-swarm#secrets)).

- Add a `README.md` file with:

  - Basic description of the project scope

  - Minimal architecture design

  - Guide for contributors

  - Coding standard (When it is not covered by an automated CI process)

- CI/CD from the beginning

  - Setup CI/CD and all environments even before adding any real functionality

  - Branch protection rules to avoid CI bypass

  - Example automated test even before adding any real functionality
