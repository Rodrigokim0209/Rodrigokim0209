## Hi there 👋
My name is ("Rodrigo Camacho")
   (what's your name")
<!--
**Rodrigokim0209/Rodrigokim0209** is a ✨ _special_ ✨ repository because its `README.md` (this file) appears on your GitHub profile.

Here are some ideas to get you started:

- 🔭 I’m currently working on ...
- 🌱 I’m currently learning ...
- 👯 I’m looking to collaborate on ...
- 🤔 I’m looking for help with ...
- 💬 Ask me about ...
- 📫 How to reach me: ...
- 😄 Pronouns: ...
- ⚡ Fun fact: ...
-->
MainActivity.kt
name: Update Versions / Dependencies

on:
  workflow_dispatch:
env:
  compose_store_password: ${{ secrets.COMPOSE_STORE_PASSWORD }}
  compose_key_alias: ${{ secrets.COMPOSE_KEY_ALIAS }}
  compose_key_password: ${{ secrets.COMPOSE_KEY_PASSWORD }}
jobs:
  build:
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@v4
    - name: Copy CI gradle.properties
      run: mkdir -p ~/.gradle ; cp .github/ci-gradle.properties ~/.gradle/gradle.properties
    - name: set up JDK 17
      uses: actions/setup-java@v4
      with:
        java-version: 17
        distribution: 'zulu'
        cache: gradle

    - name: Update dependencies
      run: ./scripts/updateDeps.sh
    - name: Duplicate version configuration
      run: ./scripts/duplicate_version_config.sh
    - name: Create pull request
      id: cpr
      uses: peter-evans/create-pull-request@v7
      with:
        token: ${{ secrets.PAT }}
        commit-message: 🤖 Update Dependencies
        committer: compose-devrel-github-bot <compose-devrel-github-bot@google.com>
        author: compose-devrel-github-bot <compose-devrel-github-bot@google.com>
        signoff: false
        branch: bot-update-deps
        delete-branch: true
        title: '🤖 Update Dependencies'
        body: Updated depedencies
        reviewers: ${{ github.actor }}