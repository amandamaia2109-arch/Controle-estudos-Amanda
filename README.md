import React, { useMemo, useState, useEffect } from "react";
import { Card, CardContent, CardHeader, CardTitle } from "@/components/ui/card";
import { Button } from "@/components/ui/button";
import { Progress } from "@/components/ui/progress";
import { Badge } from "@/components/ui/badge";
import { Tabs, TabsContent, TabsList, TabsTrigger } from "@/components/ui/tabs";
import { Select, SelectContent, SelectItem, SelectTrigger, SelectValue } from "@/components/ui/select";
import { Textarea } from "@/components/ui/textarea";
import { Input } from "@/components/ui/input";
import { Clock3, BookOpen, Scale, RotateCcw, PlayCircle, FileText, Gavel, Trophy } from "lucide-react";

const weeklyPlan = {
  segunda: [
    { duration: 120, subject: "Direito Penal", mode: "Teoria + CP + Lei Penal Especial", group: "penal" },
    { duration: 120, subject: "Direito Processual Penal", mode: "Teoria + CPP", group: "penal" },
    { duration: 60, subject: "Direito Penal / Processo Penal", mode: "Questões vinculadas", group: "penal" },
    { duration: 30, subject: "Informativos Penais", mode: "Jurisprudência vinculada", group: "penal" },
    { duration: 30, subject: "Revisão Penal", mode: "Revisão vinculada", group: "penal" },
  ],
  terca: [
    { duration: 120, subject: "Direito Constitucional", mode: "Teoria + CF", group: "publico" },
    { duration: 120, subject: "Direito Administrativo", mode: "Teoria + Leis Administrativas", group: "publico" },
    { duration: 60, subject: "Constitucional / Administrativo", mode: "Questões vinculadas", group: "publico" },
    { duration: 30, subject: "Informativos STF", mode: "Jurisprudência vinculada", group: "publico" },
    { duration: 30, subject: "Revisão Constitucional / Administrativo", mode: "Revisão vinculada", group: "publico" },
  ],
  quarta: [
    { duration: 120, subject: "Direito Processual Coletivo", mode: "Teoria + Microssistema Coletivo", group: "coletivo" },
    { duration: 120, subject: "Improbidade Administrativa", mode: "Teoria + ACP + conexão com leis especiais", group: "coletivo" },
    { duration: 60, subject: "Processo Coletivo / Improbidade", mode: "Questões vinculadas", group: "coletivo" },
    { duration: 30, subject: "Informativos de Coletivo / Patrimônio Público", mode: "Jurisprudência vinculada", group: "coletivo" },
    { duration: 30, subject: "Revisão Coletiva", mode: "Revisão vinculada", group: "coletivo" },
  ],
  quinta: [
    { duration: 120, subject: "Direito Civil", mode: "Teoria + Código Civil", group: "civil" },
    { duration: 120, subject: "Direito Processual Civil", mode: "Teoria + CPC", group: "civil" },
    { duration: 60, subject: "Civil / Processo Civil", mode: "Questões vinculadas", group: "civil" },
    { duration: 30, subject: "Informativos Cíveis", mode: "Jurisprudência vinculada", group: "civil" },
    { duration: 30, subject: "Revisão Civil", mode: "Revisão vinculada", group: "civil" },
  ],
  sexta: [
    { duration: 120, subject: "Ambiental / Consumidor / ECA", mode: "Teoria em rodízio", group: "difusos" },
    { duration: 120, subject: "Direitos Humanos", mode: "Sistema Interamericano + legislação", group: "difusos" },
    { duration: 60, subject: "Difusos / Direitos Humanos", mode: "Questões vinculadas", group: "difusos" },
    { duration: 30, subject: "Informativos de Difusos e Direitos Humanos", mode: "Jurisprudência vinculada", group: "difusos" },
    { duration: 30, subject: "Revisão Difusos", mode: "Revisão vinculada", group: "difusos" },
  ],
  sabado: [
    { duration: 120, subject: "Lei Seca Penal", mode: "CP + Lei Penal Especial", group: "sabado" },
    { duration: 120, subject: "Lei Seca Constitucional / CPC", mode: "CF + CPC", group: "sabado" },
    { duration: 60, subject: "Questões Mistas", mode: "Treino vinculado da semana", group: "sabado" },
    { duration: 30, subject: "Informativos da Semana", mode: "Consolidação jurisprudencial", group: "sabado" },
    { duration: 30, subject: "Revisão Geral", mode: "Revisão vinculada", group: "sabado" },
  ],
  domingo: [
    { duration: 60, subject: "Informativos", mode: "Leitura leve", group: "domingo" },
    { duration: 60, subject: "Revisão", mode: "Flashcards / marcações", group: "domingo" },
  ],
};

const lawRotation = [
  { week: 1, items: ["Lei de Drogas", "Crimes Hediondos"] },
  { week: 2, items: ["Organização Criminosa", "Lavagem de Dinheiro"] },
  { week: 3, items: ["Abuso de Autoridade", "Maria da Penha"] },
  { week: 4, items: ["Estatuto do Desarmamento", "Crimes Ambientais"] },
];

const subjects = [
  ["Direito Penal", 5],
  ["Direito Processual Penal", 5],
  ["Direito Constitucional", 4],
  ["Direito Administrativo", 4],
  ["Direito Civil", 4],
  ["Direito Processual Coletivo", 5],
  ["Direito Processual Civil", 5],
  ["Improbidade Administrativa", 4],
  ["Lei Penal Especial", 4],
  ["ECA", 3],
  ["Direito Eleitoral", 3],
  ["Direito Empresarial", 3],
  ["Direito Tributário", 2],
  ["Criminologia", 1],
  ["Direito Ambiental", 3],
  ["Direito do Consumidor", 3],
  ["Execução Penal", 3],
  ["Controle de Constitucionalidade", 2],
  ["Jurisprudência de Competência", 3],
  ["Direitos Humanos", 2],
  ["Informativos", 4],
  ["Teoria Geral do Ministério Público", 4],
];

const dayOrder = ["domingo", "segunda", "terca", "quarta", "quinta", "sexta", "sabado"];
const dayLabel = {
  domingo: "Domingo",
  segunda: "Segunda",
  terca: "Terça",
  quarta: "Quarta",
  quinta: "Quinta",
  sexta: "Sexta",
  sabado: "Sábado",
};

function getTodayKey() {
  const now = new Date();
  return dayOrder[now.getDay()];
}

function formatMinutes(mins) {
  const h = Math.floor(mins / 60);
  const m = mins % 60;
  if (h && m) return `${h}h ${m}min`;
  if (h) return `${h}h`;
  return `${m}min`;
}

function getWeekOfRotation() {
  const start = new Date("2026-01-05T00:00:00");
  const today = new Date();
  const diff = Math.floor((today.getTime() - start.getTime()) / (1000 * 60 * 60 * 24));
  const week = Math.floor(Math.max(diff, 0) / 7);
  return (week % 4) + 1;
}

export default function PainelEstudosMPMG() {
  const [selectedDay, setSelectedDay] = useState(getTodayKey());
  const [activeBlock, setActiveBlock] = useState(null);
  const [secondsLeft, setSecondsLeft] = useState(0);
  const [running, setRunning] = useState(false);
  const [notes, setNotes] = useState("");
  const [doneBlocks, setDoneBlocks] = useState([]);
  const [weekGoal, setWeekGoal] = useState(32);

  const todayPlan = weeklyPlan[selectedDay];
  const totalMinutes = useMemo(() => todayPlan.reduce((sum, block) => sum + block.duration, 0), [todayPlan]);
  const rotationWeek = getWeekOfRotation();
  const currentLaws = lawRotation.find((item) => item.week === rotationWeek)?.items || [];

  useEffect(() => {
    let interval;
    if (running && secondsLeft > 0) {
      interval = setInterval(() => setSecondsLeft((s) => s - 1), 1000);
    } else if (secondsLeft === 0) {
      setRunning(false);
    }
    return () => clearInterval(interval);
  }, [running, secondsLeft]);

  const completedMinutes = useMemo(() => {
    return doneBlocks.reduce((sum, idx) => sum + (todayPlan[idx]?.duration || 0), 0);
  }, [doneBlocks, todayPlan]);

  const progressValue = totalMinutes ? Math.round((completedMinutes / totalMinutes) * 100) : 0;
  const weightedSubjects = useMemo(() => subjects.sort((a, b) => b[1] - a[1]), []);

  const startTimer = (index, duration) => {
    setActiveBlock(index);
    setSecondsLeft(duration * 60);
    setRunning(true);
  };

  const pauseTimer = () => setRunning(false);
  const resumeTimer = () => secondsLeft > 0 && setRunning(true);
  const resetTimer = () => {
    if (activeBlock !== null) {
      setSecondsLeft(todayPlan[activeBlock].duration * 60);
    }
    setRunning(false);
  };

  const markDone = (index) => {
    if (!doneBlocks.includes(index)) {
      setDoneBlocks([...doneBlocks, index]);
    }
  };

  const clearDay = () => {
    setDoneBlocks([]);
    setActiveBlock(null);
    setRunning(false);
    setSecondsLeft(0);
  };

  const timerLabel = `${String(Math.floor(secondsLeft / 60)).padStart(2, "0")}:${String(secondsLeft % 60).padStart(2, "0")}`;

  return (
    <div className="min-h-screen bg-slate-50 p-4 md:p-8">
      <div className="max-w-7xl mx-auto space-y-6">
        <div className="flex flex-col md:flex-row md:items-center md:justify-between gap-4">
          <div>
            <h1 className="text-3xl font-bold tracking-tight">Painel de Estudos — MP/MG</h1>
            <p className="text-slate-600 mt-1">Cronograma inteligente, pomodoro flexível e blocos sempre vinculados à matéria.</p>
          </div>
          <div className="flex gap-2 flex-wrap">
            <Badge className="rounded-2xl px-3 py-1 text-sm">Hoje: {dayLabel[getTodayKey()]}</Badge>
            <Badge variant="secondary" className="rounded-2xl px-3 py-1 text-sm">Rotação penal especial: Semana {rotationWeek}</Badge>
          </div>
        </div>

        <div className="grid grid-cols-1 lg:grid-cols-3 gap-6">
          <Card className="rounded-2xl shadow-sm lg:col-span-2">
            <CardHeader>
              <div className="flex flex-col md:flex-row md:items-center md:justify-between gap-3">
                <CardTitle className="flex items-center gap-2"><Clock3 className="w-5 h-5" /> Planejamento do dia</CardTitle>
                <Select value={selectedDay} onValueChange={(v) => { setSelectedDay(v); setDoneBlocks([]); setActiveBlock(null); setRunning(false); setSecondsLeft(0); }}>
                  <SelectTrigger className="w-[180px] rounded-xl">
                    <SelectValue placeholder="Selecione o dia" />
                  </SelectTrigger>
                  <SelectContent>
                    {Object.keys(dayLabel).map((day) => (
                      <SelectItem key={day} value={day}>{dayLabel[day]}</SelectItem>
                    ))}
                  </SelectContent>
                </Select>
              </div>
            </CardHeader>
            <CardContent className="space-y-4">
              <div className="grid grid-cols-1 md:grid-cols-3 gap-4">
                <div className="rounded-2xl bg-white border p-4">
                  <div className="text-sm text-slate-500">Carga do dia</div>
                  <div className="text-2xl font-semibold">{formatMinutes(totalMinutes)}</div>
                </div>
                <div className="rounded-2xl bg-white border p-4">
                  <div className="text-sm text-slate-500">Concluído</div>
                  <div className="text-2xl font-semibold">{formatMinutes(completedMinutes)}</div>
                </div>
                <div className="rounded-2xl bg-white border p-4">
                  <div className="text-sm text-slate-500">Meta semanal</div>
                  <div className="flex items-center gap-2 mt-1">
                    <Input value={weekGoal} onChange={(e) => setWeekGoal(e.target.value)} className="rounded-xl w-24" />
                    <span className="text-sm text-slate-600">horas</span>
                  </div>
                </div>
              </div>

              <div>
                <div className="flex justify-between text-sm mb-2">
                  <span>Progresso do dia</span>
                  <span>{progressValue}%</span>
                </div>
                <Progress value={progressValue} className="h-3" />
              </div>

              <div className="space-y-3">
                {todayPlan.map((block, index) => {
                  const isDone = doneBlocks.includes(index);
                  const isActive = activeBlock === index;
                  return (
                    <div key={index} className={`rounded-2xl border p-4 transition ${isDone ? "bg-emerald-50 border-emerald-200" : "bg-white"}`}>
                      <div className="flex flex-col md:flex-row md:items-center md:justify-between gap-3">
                        <div>
                          <div className="flex items-center gap-2 flex-wrap">
                            <Badge variant={isDone ? "default" : "secondary"}>Bloco {index + 1}</Badge>
                            <Badge variant="outline">{formatMinutes(block.duration)}</Badge>
                            {isActive && <Badge className="bg-blue-600">Em andamento</Badge>}
                          </div>
                          <h3 className="text-lg font-semibold mt-2">{block.subject}</h3>
                          <p className="text-sm text-slate-600">{block.mode}</p>
                        </div>
                        <div className="flex gap-2 flex-wrap">
                          <Button className="rounded-xl" onClick={() => startTimer(index, block.duration)}>
                            <PlayCircle className="w-4 h-4 mr-2" /> Iniciar
                          </Button>
                          <Button variant="outline" className="rounded-xl" onClick={() => markDone(index)}>
                            Marcar feito
                          </Button>
                        </div>
                      </div>
                    </div>
                  );
                })}
              </div>
            </CardContent>
          </Card>

          <Card className="rounded-2xl shadow-sm">
            <CardHeader>
              <CardTitle className="flex items-center gap-2"><PlayCircle className="w-5 h-5" /> Pomodoro</CardTitle>
            </CardHeader>
            <CardContent className="space-y-4">
              <div className="rounded-2xl border bg-white p-6 text-center">
                <div className="text-sm text-slate-500">Timer atual</div>
                <div className="text-5xl font-bold tracking-wider mt-2">{timerLabel}</div>
                <div className="text-sm text-slate-600 mt-2">
                  {activeBlock !== null ? todayPlan[activeBlock].subject : "Escolha um bloco para começar"}
                </div>
              </div>

              <div className="grid grid-cols-3 gap-2">
                <Button variant="outline" className="rounded-xl" onClick={pauseTimer}>Pausar</Button>
                <Button variant="outline" className="rounded-xl" onClick={resumeTimer}>Retomar</Button>
                <Button variant="outline" className="rounded-xl" onClick={resetTimer}>Resetar</Button>
              </div>

              <div className="rounded-2xl border bg-white p-4">
                <div className="text-sm font-medium">Regras do seu dia</div>
                <ul className="text-sm text-slate-600 mt-2 space-y-2 list-disc pl-5">
                  <li>Sempre vincular lei seca, revisão, informativo ou questões à matéria do bloco.</li>
                  <li>Lei penal especial entra forte na segunda e sábado, e conectada na quarta.</li>
                  <li>Domingo tem só 2 horas: informativos + revisão leve.</li>
                </ul>
              </div>

              <Button variant="destructive" className="w-full rounded-xl" onClick={clearDay}>Limpar progresso do dia</Button>
            </CardContent>
          </Card>
        </div>

        <Tabs defaultValue="rotacao" className="w-full">
          <TabsList className="grid grid-cols-1 md:grid-cols-4 rounded-2xl h-auto p-1">
            <TabsTrigger value="rotacao" className="rounded-xl">Rotação</TabsTrigger>
            <TabsTrigger value="pesos" className="rounded-xl">Pesos</TabsTrigger>
            <TabsTrigger value="anotacoes" className="rounded-xl">Anotações</TabsTrigger>
            <TabsTrigger value="estrategia" className="rounded-xl">Estratégia</TabsTrigger>
          </TabsList>

          <TabsContent value="rotacao" className="mt-4">
            <div className="grid grid-cols-1 md:grid-cols-2 xl:grid-cols-4 gap-4">
              {lawRotation.map((rotation) => (
                <Card key={rotation.week} className={`rounded-2xl ${rotation.week === rotationWeek ? "border-blue-500" : ""}`}>
                  <CardHeader>
                    <CardTitle className="text-lg flex items-center gap-2"><Scale className="w-4 h-4" /> Semana {rotation.week}</CardTitle>
                  </CardHeader>
                  <CardContent>
                    <ul className="space-y-2 text-sm text-slate-700">
                      {rotation.items.map((item) => (
                        <li key={item} className="rounded-xl bg-slate-50 border px-3 py-2">{item}</li>
                      ))}
                    </ul>
                  </CardContent>
                </Card>
              ))}
            </div>
            <Card className="rounded-2xl mt-4">
              <CardHeader>
                <CardTitle>Rotação ativa agora</CardTitle>
              </CardHeader>
              <CardContent>
                <div className="flex gap-2 flex-wrap">
                  {currentLaws.map((law) => (
                    <Badge key={law} className="rounded-xl px-3 py-1">{law}</Badge>
                  ))}
                </div>
              </CardContent>
            </Card>
          </TabsContent>

          <TabsContent value="pesos" className="mt-4">
            <Card className="rounded-2xl">
              <CardHeader>
                <CardTitle className="flex items-center gap-2"><Trophy className="w-5 h-5" /> Matérias por prioridade</CardTitle>
              </CardHeader>
              <CardContent>
                <div className="grid grid-cols-1 md:grid-cols-2 xl:grid-cols-3 gap-3">
                  {weightedSubjects.map(([name, weight]) => (
                    <div key={name} className="rounded-2xl border p-4 bg-white flex items-center justify-between">
                      <span className="text-sm font-medium">{name}</span>
                      <Badge variant={weight >= 5 ? "default" : weight >= 4 ? "secondary" : "outline"}>Peso {weight}</Badge>
                    </div>
                  ))}
                </div>
              </CardContent>
            </Card>
          </TabsContent>

          <TabsContent value="anotacoes" className="mt-4">
            <Card className="rounded-2xl">
              <CardHeader>
                <CardTitle className="flex items-center gap-2"><FileText className="w-5 h-5" /> Anotações do dia</CardTitle>
              </CardHeader>
              <CardContent>
                <Textarea
                  value={notes}
                  onChange={(e) => setNotes(e.target.value)}
                  placeholder="Ex.: hoje errei bastante nulidades no CPP; revisar amanhã com 10 questões."
                  className="min-h-[220px] rounded-2xl"
                />
              </CardContent>
            </Card>
          </TabsContent>

          <TabsContent value="estrategia" className="mt-4">
            <div className="grid grid-cols-1 md:grid-cols-2 gap-4">
              <Card className="rounded-2xl">
                <CardHeader>
                  <CardTitle className="flex items-center gap-2"><BookOpen className="w-5 h-5" /> Proporção ideal</CardTitle>
                </CardHeader>
                <CardContent className="space-y-3 text-sm text-slate-700">
                  <div className="rounded-xl border p-3">50% teoria vinculada à matéria</div>
                  <div className="rounded-xl border p-3">20% jurisprudência / informativos vinculados</div>
                  <div className="rounded-xl border p-3">20% lei seca vinculada</div>
                  <div className="rounded-xl border p-3">10% revisão vinculada</div>
                </CardContent>
              </Card>

              <Card className="rounded-2xl">
                <CardHeader>
                  <CardTitle className="flex items-center gap-2"><Gavel className="w-5 h-5" /> Regra de execução</CardTitle>
                </CardHeader>
                <CardContent className="space-y-3 text-sm text-slate-700">
                  <div className="rounded-xl border p-3">Questões sempre vinculadas à matéria do dia.</div>
                  <div className="rounded-xl border p-3">Lei penal especial com peso 4 e rotação semanal.</div>
                  <div className="rounded-xl border p-3">Sábado é consolidação pesada; domingo é manutenção leve.</div>
                  <div className="rounded-xl border p-3">Nada de “lei seca genérica”: sempre matéria + lei correspondente.</div>
                </CardContent>
              </Card>
            </div>
          </TabsContent>
        </Tabs>
      </div>
    </div>
  );
}
