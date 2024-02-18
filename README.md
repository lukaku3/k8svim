# k8svim

## cheet sheets
  https://kubernetes.io/docs/reference/kubectl/quick-reference/#creating-objects

## File

```
cat >> ~/.vimrc << EOF
set expandtab
set tabstop=2
set shiftwidth=2
set number
set paste

EOF
```

```
cat >> ~/.bashrc << EOF

alias k=kubectl
export do=" --dry-run=client -o yaml "
EOF

source ~/.bashrc
```
