# auto-issue
An automation to create GitHub Issues

## Proof of concept: Creating the Issue for `use-m`

This document explains how the issue was created for the `use-m` module on GitHub.

## Steps Taken

1. Wrote a minimal reproducible sample (`minimal-test.mjs`) that demonstrates the error:

   ```js
   #!/usr/bin/env node
   /**
    * Minimal reproducible sample for dynamic module loading
    */
   import { fileURLToPath } from 'url';
   import path from 'path';
   
   // Dynamically load use-m
   const { use } = eval(
     await fetch('https://unpkg.com/use-m/use.js').then(u => u.text())
   );
   
   // Load environment variables from .env
   const { config } = await use('dotenv@16.1.4');
   config({ path: path.resolve(process.cwd(), '.env') });
   
   const __filename = fileURLToPath(import.meta.url);
   
   console.log('Current file name:', __filename);
   ```

2. Ran the sample to capture the error output.

3. Saved the script and error details in `.issue_body.md`.

4. Created the issue using the GitHub CLI:

   ```bash
   gh issue create \
     --repo link-foundation/use-m \
     --title "ReferenceError: Cannot access '__filename' before initialization in minimal-test.mjs" \
     --label bug \
     --body-file .issue_body.md
   ```

## Contents of `.issue_body.md`

```markdown
### minimal-test.mjs
\`\`\`js
#!/usr/bin/env node
/**
 * Minimal reproducible sample for dynamic module loading
 */
import { fileURLToPath } from 'url';
import path from 'path';

// Dynamically load use-m
const { use } = eval(
  await fetch('https://unpkg.com/use-m/use.js').then(u => u.text())
);

// Load environment variables from .env
const { config } = await use('dotenv@16.1.4');
config({ path: path.resolve(process.cwd(), '.env') });

const __filename = fileURLToPath(import.meta.url);

console.log('Current file name:', __filename);
\`\`\`

### Output
\`\`\`text
<anonymous_script>:183
  if (!scriptPath && typeof __filename !== 'undefined') {
  ^

ReferenceError: Cannot access '__filename' before initialization
    at makeUse (eval at <anonymous> (file:///Users/konard/Code/konard/kaiten-api-tools/minimal-test.mjs:9:17), <anonymous>:183:3)
    at use (eval at <anonymous> (file:///Users/konard/Code/konard/kaiten-api-tools/minimal-test.mjs:9:17), <anonymous>:214:19)
    at file:///Users/konard/Code/konard/kaiten-api-tools/minimal-test.mjs:14:26
    at process.processTicksAndRejections (node:internal/process/task_queues:105:5)

Node.js v22.15.0
\`\`\`
``` 
