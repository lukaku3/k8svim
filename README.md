# k8svim

## File

```
cat >> ~/.vimrc << EOF
set expandtab
set tabstop=2
set shiftwidth=2
set number

EOF
```

```
cat >> ~/.bashrc << EOF

alias k=kubectl
export do=" --dry-run=client -o yaml "
EOF

source ~/.bashrc
```
