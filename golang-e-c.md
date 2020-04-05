---
date: "2020-04-05"
title: "Golang e C"
desc: "Dicas de como fazer a ponte entre essas linguagens."
tags: [ "code" ]
---
É muito difícil configurar a linguagem Go no ambiente Windows para compilar código C. O único ambiente de compilação que o projeto leva a sério são os ports do GCC, e não o Visual Studio, que seria a ferramenta nativa. Dessa forma, realizei boa parte das travessuras desse artigo em Linux, usando o WSL com a distro Ubuntu ou CentOS. Deve funcionar em qualquer Unix da vida.


