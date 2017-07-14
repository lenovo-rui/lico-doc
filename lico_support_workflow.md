LiCO Support Workflow Chart

```flow
st=>start: Customer Request
e=>end: resolved
cond_feature=>condition: feature request?
cond_setup=>condition: Setup request?
cond_defect=>condition: LiCO defect?
contact_bp=>operation: Contact BP/PS
contact_david=>operation: Contact David Ding
contact_l1l2=>operation: Contact L1/L2 support
cond_l1l2=>condition: Can L1/L2 resolve?
contact_l3=>operation: Contact L3 support

st->cond_setup
cond_setup(yes)->contact_bp
cond_setup(no)->cond_feature
cond_feature(yes)->contact_david
cond_feature(no)->contact_l1l2
contact_l1l2->cond_l1l2
cond_l1l2(yes)->e
cond_l1l2(no)->contact_l3
contact_l3->e
```
