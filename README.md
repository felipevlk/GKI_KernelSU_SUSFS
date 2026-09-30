<div align="center">

# AntiRoot — Kernel GKI 5.10 + SukiSU-Ultra + KPM + SUSFS

**Kernel GKI _android12-5.10_ para o Moto G54 5G / G64 5G (`cancunf`)** com
SukiSU-Ultra, **KPM nativo**, SUSFS v2.3.0, NoMount e extras — focado em
**ocultação de root** (bancos, jogos e detectores).

[![KMI](https://img.shields.io/badge/KMI-5.10--android12-2f7259.svg)]()
[![Device](https://img.shields.io/badge/device-cancunf%20(Moto%20G54%2FG64)-3DDC84.svg)]()
[![Root](https://img.shields.io/badge/root-SukiSU--Ultra-5AA300.svg)]()
[![KPM](https://img.shields.io/badge/KPM-native-E67E22.svg)]()
[![SUSFS](https://img.shields.io/badge/SUSFS-v2.3.0-4c8bf5.svg)]()
[![License: GPL-3.0](https://img.shields.io/badge/license-GPL--3.0-blue.svg)](LICENSE)

</div>

> [!WARNING]
> **Faça backup do `boot` antes de flashar.** Nada aqui tem garantia de que vai
> bootar no seu aparelho. Você assume o risco. Este projeto é um **fork** de
> [WildKernels/GKI_KernelSU_SUSFS](https://github.com/WildKernels/GKI_KernelSU_SUSFS)
> mantido pela comunidade.

---

## Sobre

Build **automatizado (GitHub Actions)** de kernel **GKI** para o
[Moto G54 5G / G64 5G (`cancunf`, MediaTek MT6855)](https://en.wikipedia.org/wiki/Moto_G54_5G),
com foco no **máximo de ocultação de root** no Android 13/14.

Em cima do build do **WildKernels**, este fork adiciona:

- ✅ **KPM nativo** — a `Image` é pós-patcheada com o `kpm/patch_linux` do
  [SukiSU_patch](https://github.com/SukiSU-Ultra/SukiSU_patch), habilitando o
  loader de **KernelPatch Modules (.kpm)** de verdade (sem ser stub).
- ✅ **Compat SUSFS ↔ SukiSU** — remove o novo *su-session exec hook* que o
  SukiSU-Ultra ainda não implementa, permitindo compilar com o **SUSFS mais novo**.
- ✅ Correção do header de auth do step de metadata do workflow.

---

## Features do kernel

| Categoria | O que vem |
|---|---|
| **Root** | **SukiSU-Ultra** (branch `builtin`) |
| **KPM** | `CONFIG_KPM=y` + `Image` pós-patcheada (KPM real) |
| **Ocultação** | **SUSFS v2.3.0** (sus_path/sus_mount/sus_kstat/spoof_uname/open_redirect/sus_map) |
| **Mounts** | **NoMount** (VFS path redirection — metamódulo) |
| **Segurança** | **Baseband Guard** (BBG) |
| **Rede** | WireGuard, BBR, IP_SET, CIFS |
| **DFS** | **DroidSpaces-OSS** (container runtime) |
| **Performance** | NTSync |
| **Ocultação extra** | Ptrace Leak Fix, Unicode Fix |
| **BPF** | BTF / eBPF / FUSE-BPF |
| **FS** | TMPFS xattr / POSIX ACLs |

KMI alvo: **`5.10-android12`** · kernel final: **`5.10.269-android12`** · page size **4k**.

---

## Como compilar (fork + GitHub Actions)

Não precisa de PC potente — o build roda na nuvem.

### 1. Fork
1. Abra este repositório e clique em **Fork**.
2. No seu fork: **Actions** → **"I understand my workflows, enable them"**.
3. **Settings → Actions → General → Workflow permissions → Read and write → Save**.

### 2. Rodar o build
**Actions → Build Kernels → Run workflow** com:

| Campo | Valor |
|---|---|
| Release Type | `CI` |
| Use cache | `false` (1ª vez) |
| Kernel Version | **`5.10.x-android12`** |
| Patch date (`os_patch_level`) | **`lts`** |
| ARM64 page size | `4k` |
| Kernel Branding | `Wild` |
| Commit mode | `verified` |
| **Root Flavor** | **`SukiSU-Ultra`** |
| SUSFS / NoMount / Baseband Guard / Networking / DroidSpaces / NTSync / Ptrace / Unicode / BPF | ✅ `true` |
| Performance | `true` (opcional) |
| Test release notes | `false` |

> ⚠️ **Não deixe nada em `All`.** O `Root Flavor` **tem** que ser `SukiSU-Ultra`
> (é o único que tem KPM).

Ao terminar (~15–40 min), baixe o **`...-AnyKernel3`** em **Artifacts**.

### 3. (Opcional) Build local
Veja [`docs/`](docs/) — o caminho via GitHub Actions é bem mais confiável.

---

## Como flashar (Moto G54 / cancunf)

> Faça backup do `boot` **antes** de tudo. Sempre.

### Jeito fácil — Kernel Flasher
1. Instale o **Kernel Flasher** (precisa de root).
2. **Flash AnyKernel3** → selecione o `.zip` do build → reinicie.

### Se o fastboot reclamar (`Preflash validation failed`)
A Motorola **bloqueia `fastboot flash boot`** mesmo com bootloader desbloqueado.
O contorno que funciona é o **fastbootd** (userspace):

```bash
adb reboot fastboot          # entra no fastbootd (não no bootloader)
fastboot devices             # deve listar como "fastbootd"
fastboot flash boot boot.img # OKAY (sem preflash validation)
fastboot reboot
```

Ou use o **Kernel Flasher** / **restore do boot** se der bootloop.

### Restaurar se der bootloop
```bash
fastboot flash boot boot-backup.img
# ou: Kernel Flasher → Restore
```

---

## Stack de ocultação recomendada (módulos)

O kernel é a **base**. Para esconder root de banco/jogo de verdade, combine:

| Camada | Módulo |
|---|---|
| Zygisk | **ZygiskNext** (ou ReZygisk) + **TreatWheel** |
| SUSFS userspace | **BRENE** |
| TEE / Integridade | **TEESimulator-RS** + **keybox** (+ PIF) |
| App list | **HMA-OSS** |
| Props | **VBMeta Disguiser** |
| Manager | **SukiSU-Ultra Manager** (ou KernelSU-Next spoofed) |

> Nada disso "garante" passar em banco (é corrida armamentista) — mas é o
> estado da arte.

---

## Contribuindo

Pull requests são **muito bem-vindos** (correções, suporte a outros KMI,
documentação, novos KPMs).

1. **Fork** este repositório.
2. Crie um branch: `git checkout -b minha-melhoria`.
3. Commit + push.
4. Abra um **Pull Request** descrevendo o que mudou e como testou.
5. Também aceito **Issues** com bugs/ideias.

> Como é um **fork**, PRs abertos *neste* repositório funcionam normalmente
> (o fork é sua base). Para mandar melhorias pro WildKernels upstream,
> abra o PR no repositório original.

---

## Créditos

Base: **[WildKernels/GKI_KernelSU_SUSFS](https://github.com/WildKernels/GKI_KernelSU_SUSFS)** (TheWildJames et al.).

- **SukiSU-Ultra** — [SukiSU-Ultra/SukiSU-Ultra](https://github.com/SukiSU-Ultra/SukiSU-Ultra)
- **SUSFS** — [simonpunk/susfs4ksu](https://gitlab.com/simonpunk/susfs4ksu)
- **KPM (KernelPatch)** — [bmax121/KernelPatch](https://github.com/bmax121/KernelPatch) · [SukiSU-Ultra/SukiSU_patch](https://github.com/SukiSU-Ultra/SukiSU_patch)
- **NoMount** — [maxsteeel/nomount](https://github.com/maxsteeel/nomount)
- **Baseband Guard** — [vc-teahouse/Baseband-guard](https://github.com/vc-teahouse/Baseband-guard)
- **DroidSpaces-OSS** — [ravindu644/Droidspaces-OSS](https://github.com/ravindu644/Droidspaces-OSS)
- **Kernel Patches** — [WildKernels/kernel_patches](https://github.com/WildKernels/kernel_patches)
- **AnyKernel3** — [osm0sis/AnyKernel3](https://github.com/osm0sis/AnyKernel3)
- **KPM integration reference** — [zzh20188/GKI_KernelSU_SUSFS](https://github.com/zzh20188/GKI_KernelSU_SUSFS), [ShirkNeko/Action_OnePlus_MKSU_SUSFS](https://github.com/ShirkNeko/Action_OnePlus_MKSU_SUSFS), [Lokitla/GKI_SUKISU_SUSFS_for_Lokita](https://github.com/Lokitla/GKI_SUKISU_SUSFS_for_Lokita)
- **Comunidade cancunf** — cyberknight777, sarthakroy2002 e o canal [@motorolag54updates](https://t.me/motorolag54updates)

---

## Licença

- Kernel (`kernel/`): **GPL-2.0-only**
- Restante do repositório: **GPL-3.0-or-later** (ver [LICENSE](LICENSE) e [THIRD_PARTY_NOTICES.md](THIRD_PARTY_NOTICES.md))
