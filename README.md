# CI-CD-Workflow-Challenge Issues

-> Exposed Secrets (*****CRITICAL******)

1. Line 15: ACR username value is hardcoded `svc-acr-push` instead of being mounted from Github Actions secrets `${{ secrets.ACR_USERNAME }}`. The account name is visible to anyone with repo access.

2. Line 16: ACR password value is hardcoded 'P@ssw0rd!2025-ACR' instead of `${{ secrets.ACR_PASSWORD }}`. This compromises the Azure Registry, so anyone will be able to push Docker images to the registry.

3. Line 47: Credentials are again hardcoded in the docker login command. These credentials will also appear in the workflow logs, compromising the user's identity.

4. Line 82: SonarQube token is also hardcoded instead of `${{ secrets.SONAR_TOKEN }}`.

5. Line 121: ArgoCD username is hardcoded as `admin`.

-> Pipeline Errors (*****HIGH*****)

6. Line 20: `build-and-push` job has no `needs: test`. This means both jobs can run parallelly, so the docker image is built and pushed to ACR even if any test cases fail.

7. Line 92: `deploy-dev` job depends only on `build-and-push`, which itself doesn't depend on `test`. So untested code will get deployed to the dev environment.

8. Line 6: The workflow trigger is set to `branches: '*'` (all branches). So every feature branch push triggers the full pipeline. This can destabilize the dev environment, and create unnecessary builds, wasting time. Only mention the branch which needs to be deployed to dev.

-> Typos (*****MEDIUM*****)

9. Line 28: `actions/checkout@4` is missing the `v` prefix. It should be `actions/checkout@v4`. GitHub Actions cannot resolve the action. So the entire job will fail at this step.

10. Line 44: `${{ env.ACR_REGISTY }}` has a typo. It's missing the letter `R`. It should be `ACR_REGISTRY`. Variable will point to an empty string.

11. Line 110: `sed` targets `overlays/development/deploy.yml`. This is an incorrect path. The standard path is `overlays/dev/deployment.yaml`.

-> Best Practices (*****MEDIUM*****)

12. Line 42: Image tag is hardcoded to `"latest"` instead of using the commit SHA. So every build overwrites the same tag, so there is no way to trace which commit is running. 
