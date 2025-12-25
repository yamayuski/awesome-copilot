# AGENTS.md

## プロジェクト概要

Awesome GitHub Copilotリポジトリは、さまざまなドメイン、言語、ユースケースにわたってGitHub Copilot体験を強化するために設計された、カスタムエージェント、プロンプト、インストラクションのコミュニティ主導のコレクションです。プロジェクトには以下が含まれます：

- **Agents（エージェント）** - MCPサーバーと統合する特化したGitHub Copilotエージェント
- **Prompts（プロンプト）** - コード生成と問題解決のためのタスク固有のプロンプト
- **Instructions（インストラクション）** - 特定のファイルパターンに適用されるコーディング標準とベストプラクティス
- **Skills（スキル）** - 特化したタスクのためのインストラクションとバンドルされたリソースを含む自己完結型のフォルダ
- **Collections（コレクション）** - 特定のテーマとワークフローを中心に整理されたキュレートされたコレクション

## リポジトリ構造

```
.
├── agents/           # カスタムGitHub Copilotエージェント定義（.agent.mdファイル）
├── prompts/          # タスク固有のプロンプト（.prompt.mdファイル）
├── instructions/     # コーディング標準とガイドライン（.instructions.mdファイル）
├── skills/           # エージェントスキルフォルダ（それぞれSKILL.mdとオプションのバンドルされたアセット）
├── collections/      # リソースのキュレートされたコレクション（.mdファイル）
├── docs/             # 異なるリソースタイプのドキュメント
├── eng/              # ビルドと自動化スクリプト
└── scripts/          # ユーティリティスクリプト
```

## セットアップコマンド

```bash
# 依存関係をインストール
npm ci

# プロジェクトをビルド（README.mdを生成）
npm run build

# コレクションマニフェストを検証
npm run collection:validate

# 新しいコレクションを作成
npm run collection:create -- --id <collection-id> --tags <tags>

# エージェントスキルを検証
npm run skill:validate

# 新しいスキルを作成
npm run skill:create -- --name <skill-name>
```

## 開発ワークフロー

### Working with Agents, Prompts, Instructions, and Skills

All agent files (`*.agent.md`), prompt files (`*.prompt.md`), and instruction files (`*.instructions.md`) must include proper markdown front matter. Agent Skills are folders containing a `SKILL.md` file with frontmatter and optional bundled assets:

#### Agent Files (*.agent.md)
- Must have `description` field (wrapped in single quotes)
- File names should be lower case with words separated by hyphens
- Recommended to include `tools` field
- Strongly recommended to specify `model` field

#### Prompt Files (*.prompt.md)
- Must have `agent` field (value should be `'agent'` wrapped in single quotes)
- Must have `description` field (wrapped in single quotes, not empty)
- File names should be lower case with words separated by hyphens
- Recommended to specify `tools` if applicable
- Strongly recommended to specify `model` field

#### Instruction Files (*.instructions.md)
- Must have `description` field (wrapped in single quotes, not empty)
- Must have `applyTo` field specifying file patterns (e.g., `'**.js, **.ts'`)
- File names should be lower case with words separated by hyphens

#### Agent Skills (skills/*/SKILL.md)
- Each skill is a folder containing a `SKILL.md` file
- SKILL.md must have `name` field (lowercase with hyphens, matching folder name, max 64 characters)
- SKILL.md must have `description` field (wrapped in single quotes, 10-1024 characters)
- Folder names should be lower case with words separated by hyphens
- Skills can include bundled assets (scripts, templates, data files)
- Bundled assets should be referenced in the SKILL.md instructions
- Asset files should be reasonably sized (under 5MB per file)
- Skills follow the [Agent Skills specification](https://agentskills.io/specification)

### Adding New Resources

When adding a new agent, prompt, instruction, or skill:

**For Agents, Prompts, and Instructions:**
1. Create the file with proper front matter
2. Add the file to the appropriate directory
3. Update the README.md by running: `npm run build`
4. Verify the resource appears in the generated README

**For Skills:**
1. Run `npm run skill:create` to scaffold a new skill folder
2. Edit the generated SKILL.md file with your instructions
3. Add any bundled assets (scripts, templates, data) to the skill folder
4. Run `npm run skill:validate` to validate the skill structure
5. Update the README.md by running: `npm run build`
6. Verify the skill appears in the generated README

### Testing Instructions

```bash
# Run all validation checks
npm run collection:validate
npm run skill:validate

# Build and verify README generation
npm run build

# Fix line endings (required before committing)
bash scripts/fix-line-endings.sh
```

Before committing:
- Ensure all markdown front matter is correctly formatted
- Verify file names follow the lower-case-with-hyphens convention
- Run `npm run build` to update the README
- **Always run `bash scripts/fix-line-endings.sh`** to normalize line endings (CRLF → LF)
- Check that your new resource appears correctly in the README

## Code Style Guidelines

### Markdown Files
- Use proper front matter with required fields
- Keep descriptions concise and informative
- Wrap description field values in single quotes
- Use lower-case file names with hyphens as separators

### JavaScript/Node.js Scripts
- Located in `eng/` and `scripts/` directories
- Follow Node.js ES module conventions (`.mjs` extension)
- Use clear, descriptive function and variable names

## Pull Request Guidelines

When creating a pull request:

1. **README updates**: New files should automatically be added to the README when you run `npm run build`
2. **Front matter validation**: Ensure all markdown files have the required front matter fields
3. **File naming**: Verify all new files follow the lower-case-with-hyphens naming convention
4. **Build check**: Run `npm run build` before committing to verify README generation
5. **Line endings**: **Always run `bash scripts/fix-line-endings.sh`** to normalize line endings to LF (Unix-style)
6. **Description**: Provide a clear description of what your agent/prompt/instruction does
7. **Testing**: If adding a collection, run `npm run collection:validate` to ensure validity

### Pre-commit Checklist

Before submitting your PR, ensure you have:
- [ ] Run `npm install` (or `npm ci`) to install dependencies
- [ ] Run `npm run build` to generate the updated README.md
- [ ] Run `bash scripts/fix-line-endings.sh` to normalize line endings
- [ ] Verified that all new files have proper front matter
- [ ] Tested that your contribution works with GitHub Copilot
- [ ] Checked that file names follow the naming convention

### Code Review Checklist

For prompt files (*.prompt.md):
- [ ] Has markdown front matter
- [ ] Has `agent` field (value should be `'agent'` wrapped in single quotes)
- [ ] Has non-empty `description` field wrapped in single quotes
- [ ] File name is lower case with hyphens
- [ ] Includes `model` field (strongly recommended)

For instruction files (*.instructions.md):
- [ ] Has markdown front matter
- [ ] Has non-empty `description` field wrapped in single quotes
- [ ] Has `applyTo` field with file patterns
- [ ] File name is lower case with hyphens

For agent files (*.agent.md):
- [ ] Has markdown front matter
- [ ] Has non-empty `description` field wrapped in single quotes
- [ ] File name is lower case with hyphens
- [ ] Includes `model` field (strongly recommended)
- [ ] Considers using `tools` field

For skills (skills/*/):
- [ ] Folder contains a SKILL.md file
- [ ] SKILL.md has markdown front matter
- [ ] Has `name` field matching folder name (lowercase with hyphens, max 64 characters)
- [ ] Has non-empty `description` field wrapped in single quotes (10-1024 characters)
- [ ] Folder name is lower case with hyphens
- [ ] Any bundled assets are referenced in SKILL.md
- [ ] Bundled assets are under 5MB per file

## Contributing

This is a community-driven project. Contributions are welcome! Please see:
- [CONTRIBUTING.md](CONTRIBUTING.md) for contribution guidelines
- [CODE_OF_CONDUCT.md](CODE_OF_CONDUCT.md) for community standards
- [SECURITY.md](SECURITY.md) for security policies

## MCP Server

The repository includes an MCP (Model Context Protocol) Server that provides prompts for searching and installing resources directly from this repository. Docker is required to run the server.

## License

MIT License - see [LICENSE](LICENSE) for details
