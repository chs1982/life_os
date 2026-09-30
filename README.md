# life_os
App para controle e rastreio de habitos

Life OS

Sistema pessoal para organizar o dia a dia em um só lugar: rotinas, hábitos vitais, caixa do mês, ambiente e os nove pilares da vida. Funciona no navegador e pode ser instalado no celular como app (PWA), inclusive offline.

Todos os dados ficam no próprio aparelho. Não há conta, servidor de dados nem sincronização automática.

O que ele faz

O app tem cinco telas, acessíveis pela barra inferior no celular e pelo menu lateral no computador.

Tela	Para que serve
Painel	Visão do dia: bloco de treino, hidratação, hábitos vitais, caixa, constância das últimas 4 semanas, relacionamentos em atraso e o motor de insights. Também concentra o backup dos dados.
Rotinas	Rotina matinal, ritual noturno e higiene do sono, mais o registro diário de água, dor lombar, humor, energia e horas de sono.
Finanças	Lançamentos de receitas e despesas (com categoria, método e parcelamento), extrato, resumo do mês corrente e contratos/dívidas com saldo pago.
Ambiente	Cômodo-foco da semana e checklists de casa, ambientes digitais e espaço de treino, com meta de tempo de tela.
Pilares	Nota para cada um dos nove pilares (Saúde Física, Saúde Mental, Finanças, Carreira/Propósito, Relacionamento Amoroso, Família, Ambiente Físico, Evolução Pessoal, Lazer & Descanso), CRM de relacionamentos e metas com subtarefas.
Instalar no celular

O Life OS é um PWA. Depois de publicado em uma URL HTTPS:

iPhone: abra a URL no Safari, toque em Compartilhar e depois em Adicionar à Tela de Início. Um tutorial passo a passo fica em SUA-URL/?install=1&platform=ios.
Android: abra no Chrome, toque no menu e em Instalar app.

Uso offline. Abra o app uma vez com internet. A partir daí as páginas e os arquivos ficam salvos e ele abre sem conexão. Quando sai uma versão nova, aparece o aviso "Nova versão disponível" com o botão Atualizar.

Seus dados e o backup

Os dados são gravados no localStorage do navegador (chave LIFE_OS_DB_V2). Isso traz duas consequências:

Limpar os dados do navegador ou desinstalar o app apaga tudo.
Cada lugar tem os seus dados: no iPhone, o app instalado na tela inicial e o Safari têm armazenamento separado. Computador e celular também não se sincronizam sozinhos.

Para fazer backup ou levar os dados de um aparelho para outro, use o card Sincronização local, no Painel:

Exportar baixa um arquivo LIFE_OS_BACKUP_AAAA-MM-DD.json.
Importar restaura a partir desse arquivo (aceita também backups do Life OS original).
Demo volta aos dados de exemplo.

Recomendação: exporte um backup por semana e antes de qualquer limpeza do navegador.

Tecnologias
React 19 e TanStack Start (roteamento e SSR)
Vite 8 e Nitro (build para Vercel)
Tailwind CSS 4, Radix UI e lucide-react
Zustand com persistência local
Recharts, para os gráficos
Service worker próprio (public/sw.js) para o modo offline
Rodando localmente

Requisitos: Node.js 22 ou superior e npm.

bash
npm install
npm run dev        # http://localhost:8080

O service worker fica desligado no dev de propósito. Para testar o PWA e o offline, gere o build e use o preview:

bash
npm run build
npm run preview    # http://127.0.0.1:8081

No Chrome, abra o DevTools > Application e confira o Manifest (ícones 192, 512 e maskable) e o Service Workers (status "activated"). Marque Offline e recarregue para testar.

Windows (PowerShell)

Os scripts dev, build e preview passam por scripts/with-app-env.mjs, que não encontra o vite.cmd no Windows (erro spawn vite ENOENT). Há duas saídas.

Opção 1: chamar o Vite direto.

powershell
$env:VITE_AUTH_ENABLED = "false"
npx vite build
npx vite preview

Opção 2: corrigir o script, uma vez. Em scripts/with-app-env.mjs, troque

js
const child = spawn(command, args, { stdio: "inherit", env });

por

js
const child = spawn(command, args, { stdio: "inherit", env, shell: process.platform === "win32" });

Depois disso npm run build e npm run preview funcionam normalmente.

npm run preview:restart só roda dentro do sandbox onde o projeto foi criado e não funciona no seu computador.

Scripts
Comando	O que faz
npm run dev	Servidor de desenvolvimento na porta 8080
npm run build	Build de produção (preset Vercel)
npm run preview	Serve o build em 127.0.0.1:8081
npm run typecheck	Verificação de tipos com tsc
npm run lint	ESLint
npm run format	Prettier
npm test	Testes dos scripts e da camada de dados
Publicando

O projeto está configurado para a Vercel (vercel.json e preset do Nitro). Ao publicar por conta própria:

Cadastre a variável de ambiente VITE_AUTH_ENABLED=false nas configurações do projeto. Sem ela o padrão é login ligado e o app mostraria uma tela de entrada.
Não cadastre DATABASE_URL. Com o login desligado e o banco ligado, o servidor se recusa a iniciar.
Acesse a URL final pelo celular e instale como descrito em Instalar no celular.
Estrutura
public/
  sw.js                    service worker (offline)
  icons/                   ícones do PWA (192, 512, maskable)
scripts/
  grok-pwa-*.mjs           manifest e tags de PWA (não apagar)
  with-app-env.mjs         carrega as variáveis VITE_* antes de iniciar o Vite
server/middleware/         página de instalação e manifest (não apagar)
src/
  routes/                  __root.tsx e index.tsx
  components/lifeos/       telas e componentes do app
  components/pwa-register.tsx   registro do service worker
  lib/lifeos/
    store.ts               estado global e persistência (Zustand)
    engine.ts              cálculos: constância, saldo, insights, pilares
    types.ts               modelo de dados (LifeDB v2)
    constants.ts           metas, categorias, checklists e pilares
    migrate.ts             importação e migração de backups antigos
    seed.ts                dados de exemplo
Personalizando
Checklists, categorias, cômodos e pilares: src/lib/lifeos/constants.ts.
Metas do dia (água, sono, tempo de tela, limites de dor): também em constants.ts.
Cores e tipografia: src/styles.css.
Forçar atualização do app em todos os aparelhos: mude CACHE_VERSION em public/sw.js.
Aviso

O Life OS é uma ferramenta de organização pessoal. Os alertas e insights que ele mostra não substituem orientação médica, financeira ou profissional.
