---
date: "2019-09-07"
title: "Vcpkg: Atualizando Lib Asio"
desc: "Guia mais ou menos simples de como atualizar/modificar uma lib/pacote disponível no vcpkg."
categories: [ "code" ]
---
Hoje tive que compilar a versão 1.13.0 do Asio para Windows, mas o [vcpkg](/vcpkg) não suporta essa versão ainda, apesar de suportar uma versão (1.12.2.2). Daí entra os problemas que todo programador Windows tem para manter bibliotecas de terceiro compilando em seu ambiente, mas agora com o vcpkg isso nem é tão difícil assim. Vamos lá.

Primeiro de tudo, os pacotes disponíveis no vcpkg podem não ser os disponíveis no [branch oficial](https://github.com/microsoft/vcpkg), que é apenas uma base, que está sendo atualizado e mantido por uma equipe grande que responde os issues, é verdade, mas nem sempre possui as versões que precisamos no dia-a-dia. Para adicionar ou modificar os pacotes deve-se mexer na pasta **port** do projeto. Dentro dela há uma pasta para cada pacote disponível.

É lá que fica a pasta asio, com seus quatro arquivos: asio-config.cmake, CMakeLists.txt, CONTROL e portfile.cmake. No CONTROL temos o sumário do pacote (nome, descrição, versão), no asio-config.cmake a receita CMake para fazer o build e em CMakeLists.txt como instalar. Isso varia de pacote para pacote, mas no caso de libs como a asio ela fica no GitHub, então em algum lugar nas instruções de instalação (aqui no caso em portfile.cmake) você irá encontrar o uso da função vcpkg_from_github.

```
vcpkg_from_github(
    OUT_SOURCE_PATH SOURCE_PATH
    REPO chriskohlhoff/asio
    REF asio-1-12-2
    SHA512 7c2e213ff154bb2e5776b37906d437a62206f973316c94706e6d42e3c2f0866e7d97f3e40225ab5f28bf2c4a33fa0b38a4b75421aef86ddf9f2da0811caa2d00
    HEAD_REF master
)
```
