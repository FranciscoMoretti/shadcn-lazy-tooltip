## Shadcn Lazy Tooltip

Lazy-mount the shadcn/ui `Tooltip` tree only on first hover/touch. This removes the initial render cost of the entire tooltip/provider/popper stack until a user actually needs it.

<img width="1316" height="764" alt="lazy_tooltip" src="https://github.com/user-attachments/assets/770338e4-4359-405b-90a6-cf30cb5cfe9c" />


<!-- If you don’t have the image yet, keep the line above and drop a placeholder below -->
<!-- <img alt="Profiler before vs after" src="https://placehold.co/1600x900?text=Profiler:+Tooltip+vs+LazyTooltip" /> -->

---

### Why
- **Avoids eager mounting** (provider/popup/portal elements on first paint)
- **Cuts initial render time** when many tooltips exist (lists, grids)
- **Zero behavior change** (after first hover)

---

### Install

Prereq: you already use shadcn/ui and have its `Tooltip` component.

- Add shadcn Tooltip (if missing):

```bash
bunx shadcn@latest add tooltip
```

- Create `components/lazy-tooltip.tsx` with:

```tsx
'use client';

import { useState } from 'react';
import {
  Tooltip,
  TooltipContent,
  TooltipTrigger,
} from '@/components/ui/tooltip';

export function LazyTooltip({
  children,
  content,
  asChild = true,
}: {
  children: React.ReactElement;
  content: React.ReactNode;
  asChild?: boolean;
}) {
  const [enabled, setEnabled] = useState(false);

  const triggerProps = {
    onPointerEnter: () => setEnabled(true),
    onTouchStart: () => setEnabled(true),
  } as const;

  if (!enabled) {
    // Clone to attach events without mounting Tooltip tree
    return { ...children, props: { ...children.props, ...triggerProps } };
  }

  return (
    <Tooltip>
      <TooltipTrigger asChild={asChild}>
        {
          // Attach the same listeners so once active, hover continues to work
          { ...children, props: { ...children.props, ...triggerProps } }
        }
      </TooltipTrigger>
      <TooltipContent>{content}</TooltipContent>
    </Tooltip>
  );
}
```

---

### Usage

```tsx
import { Button } from '@/components/ui/button';
import { LazyTooltip } from '@/components/lazy-tooltip';

export function Example() {
  return (
    <LazyTooltip content="This mounts on first hover">
      <Button variant="secondary">Hover me</Button>
    </LazyTooltip>
  );
}
```


---

### API

- `children: React.ReactElement` — element to trigger the tooltip
- `content: React.ReactNode` — tooltip body
- `asChild?: boolean = true` — forwarded to shadcn `TooltipTrigger`


---

### License

MIT
