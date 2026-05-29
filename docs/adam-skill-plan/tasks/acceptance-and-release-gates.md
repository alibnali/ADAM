# Acceptance and Release Gates

## Gate A — Foundation Complete
- product charter approved ✅
- canonical request/result schemas approved
- layer/unit/failure policies documented
- prompt governance documented

## Gate B — Kernel Ready
- classifier + normalizer يعملان على عينات متعددة
- drawing primitives/macros ثابتة schema
- MCP adapter contract موثق ومختبر نظرياً

## Gate C — First Production Slice Ready
- isolated footing end-to-end pass
- report + drawing + validation log متسقة
- reviewer feedback incorporated

## Gate D — Expansion Allowed
- Gate C passed
- shared symbol library موجودة
- لا يوجد duplication في policies

## Gate E — Release Ready
- Gate D passed
- golden tests تمر جميعها
- human review protocol documented and used
- failure recovery paths موثقة ومختبرة
- skill package منظمة وقابلة للنشر
