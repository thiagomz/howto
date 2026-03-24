## 🎉 IRPF 2026 funcionando!

Que jornada de debugging! Resumo da causa raiz:

* * *

### O que causou o problema

O instalador do IRPF 2026 criou um **symlink quebrado**:

```
Contents/MacOS/libjli.dylib → ../Home/lib/jli/libjli.dylib  ❌
```

O subdiretório `jli/` não existia — o arquivo real estava em `Home/lib/libjli.dylib`. O macOS Sequoia com Gatekeeper mais rígido não tolerou o symlink inválido, e o launcher falhava silenciosamente exibindo apenas o erro genérico de "JVM não encontrada".

### Solução final aplicada

1.  Removido o symlink quebrado
2.  Copiado o `libjli.dylib` real para `Contents/MacOS/`
3.  Reassinado o bundle e o app com `codesign --force --deep --sign -`

* * *

### 💡 Para referência futura

Se o IRPF for atualizado e o problema voltar, o fix rápido é:

```bash
sudo rm "/Applications/IRPF2026/.install4j/jre.bundle/Contents/MacOS/libjli.dylib"
sudo cp "/Applications/IRPF2026/.install4j/jre.bundle/Contents/Home/lib/libjli.dylib" \
        "/Applications/IRPF2026/.install4j/jre.bundle/Contents/MacOS/libjli.dylib"
sudo codesign --force --deep --sign - "/Applications/IRPF2026/.install4j/jre.bundle"
sudo codesign --force --deep --sign - "/Applications/IRPF2026/IRPF2026.app"
```

Bom trabalho na declaração! 🇧🇷
