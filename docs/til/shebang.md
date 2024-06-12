---
title: Difference between '#!/bin/bash' and '#!/usr/bin/env bash'
date: 2024-06-03
---

- `#!/usr/bin/env bash` searches `PATH` for `bash` whereas in `#!/bin/bash` path to `bash` must be located in `/bin`
- Use `#!/usr/bin/env bash` for more portability.
