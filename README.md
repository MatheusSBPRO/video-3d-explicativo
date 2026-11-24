# Vídeo 3D explicativo

> **Este repositório mudou para o [kit-trafego-ia](https://github.com/MatheusSBPRO/kit-trafego-ia/tree/main/ferramentas/video-3d-explicativo).** A versão daqui continua funcionando, mas não recebe mais atualização. Correções, novidades, issues e PRs agora vão para [kit-trafego-ia/ferramentas/video-3d-explicativo](https://github.com/MatheusSBPRO/kit-trafego-ia/tree/main/ferramentas/video-3d-explicativo).


Skill para **Claude Code** e **Codex** que transforma um roteiro em vídeo vertical 3D (1080x1920,
30 a 60 s), no estilo animação de cidadezinha low poly: narração por IA, legenda palavra por palavra
sincronizada com a voz, câmera que acompanha a fala, etiquetas, efeitos sonoros e marca d'água.

Tudo roda no seu computador. Não precisa de editor de vídeo nem de After Effects.

![Seis quadros do vídeo de exemplo](exemplo/quadros.jpg)

## Como funciona

O vídeo é uma página HTML com uma cena three.js. Cada coisa que acontece na tela fica presa a uma
palavra do roteiro: quando a voz diz "círculo", o círculo aparece. O whisper descobre em que segundo
cada palavra foi dita, o Playwright fotografa a cena quadro a quadro e o ffmpeg junta tudo.

```
roteiro.txt -> narração (fish.audio ou ElevenLabs) -> corte de pausas -> whisper (tempo de cada palavra)
  -> alinhar.py (legenda + tempo das palavras-chave) -> cena.html -> render.js (quadros)
  -> mix.py (efeitos sonoros) -> loudnorm -> marca d'água -> video_final.mp4
```

## Requisitos

- Node 18 ou mais novo
- Python 3.9 ou mais novo, com `numpy` (`pip install numpy`)
- ffmpeg (`brew install ffmpeg` no Mac, `apt install ffmpeg` no Linux)
- Um whisper com tempo por palavra: `pip install mlx-whisper` (Mac M1 ou mais novo),
  `pip install faster-whisper` ou `pip install openai-whisper`
- Chave de uma API de voz: `FISH_API_KEY` ([fish.audio](https://fish.audio), tem modelo que gera sem
  gastar crédito) ou `ELEVENLABS_API_KEY`
- Opcional: fonte Montserrat Bold instalada, para a marca d'água

## Instalar

```bash
git clone https://github.com/MatheusSBPRO/video-3d-explicativo.git
cd video-3d-explicativo
./instalar.sh            # confere o ambiente; a skill já vale ao rodar `claude` ou `codex` nesta pasta
./instalar.sh --global   # copia a skill para ~/.claude/skills (e ~/.codex/skills), vale em qualquer pasta
```

## Usar

Abra o Claude Code (ou o Codex) e peça:

> faz um vídeo 3D explicando por que o anúncio de raio funciona melhor que o impulsionar, com a voz <ID>

A skill copia o modelo para uma pasta nova, escreve o roteiro com as palavras-âncora, ajusta a cena,
gera a narração, renderiza e confere os quadros antes de entregar.

Para rodar à mão, sem a IA:

```bash
cp -R modelo ~/videos-3d/meu-tema
# edite ~/videos-3d/meu-tema/roteiro.txt (e as âncoras em alinhar.py, se mudar o texto)
export FISH_API_KEY=...
scripts/fazer_video.sh ~/videos-3d/meu-tema --voz <ID_DA_VOZ>
```

O ID da voz no fish.audio é o final da URL dela (`fish.audio/pt/m/<ID>/`). Para trocar só a voz
depois, rode de novo com `--voz <outro ID> --refazer-narracao`. O render leva cerca de 1 minuto para
cada 50 s de vídeo num Mac M1.

Na pasta do vídeo saem `video_final.mp4` (com a marca d'água do `wm.txt`), `video.mp4` (sem marca) e
`quadros.jpg` (seis quadros para conferir se a legenda bate com a cena).

## Direito de voz

Voz de personagem, de dublador ou de gente famosa que a comunidade sobe nas bibliotecas de voz serve
só para testar o formato. Em post, anúncio ou peça de cliente, use uma voz original da biblioteca ou
a sua própria voz clonada. Usar a voz de outra pessoa sem autorização dá processo, e anúncio com voz
de famoso costuma ser reprovado.

## Estrutura

```
.claude/skills/video-3d-explicativo/SKILL.md   instruções que a IA segue
AGENTS.md                                       ponte para o Codex
modelo/                                         projeto de exemplo (cena, alinhador, mix, render, roteiro)
scripts/fazer_video.sh                          pipeline inteiro numa pasta de vídeo
scripts/narrar.py                               narração pelo fish.audio ou pela ElevenLabs
scripts/transcrever.py                          whisper com tempo por palavra
exemplo/quadros.jpg                             quadros do vídeo de exemplo
```

## Licença

MIT. Veja [LICENCA.md](LICENCA.md).
