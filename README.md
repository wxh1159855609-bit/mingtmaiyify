import { useEffect, useState } from 'react';
import { Barcode } from './components/Barcode';
import { BiemlofenLogo } from './components/BiemlofenLogo';
import { DirectionsModal } from './components/DirectionsModal';
import { TimeSlotModal } from './components/TimeSlotModal';

const PASS_ADDRESS =
  '广州市番禺区南村镇兴业大道东608号比音勒芬服饰股份有限公司商业办公楼4楼';
const PASS_PHONE = '13129374881';
const WEEKDAY_MAP: Record<string, string> = {
  Sun: '星期日',
  Mon: '星期一',
  Tue: '星期二',
  Wed: '星期三',
  Thu: '星期四',
  Fri: '星期五',
  Sat: '星期六',
};

function getBeijingClockParts(nowMs: number) {
  const date = new Date(nowMs);

  const dateFormatter = new Intl.DateTimeFormat('en-US', {
    timeZone: 'Asia/Shanghai',
    year: 'numeric',
    month: 'numeric',
    day: 'numeric',
    weekday: 'short',
    hour: '2-digit',
    minute: '2-digit',
    second: '2-digit',
    hour12: false,
  });

  const parts = dateFormatter.formatToParts(date);
  const lookup: Record<string, string> = {};
  for (const part of parts) {
    if (part.type !== 'literal') {
      lookup[part.type] = part.value;
    }
  }

  const hour = lookup.hour === '24' ? '00' : lookup.hour;
  const weekdayLabel = WEEKDAY_MAP[lookup.weekday] ?? '';

  return {
    dateLabel: `${lookup.year}年${lookup.month}月${lookup.day}日 ${weekdayLabel}`,
    timeLabel: `${hour}:${lookup.minute}:${lookup.second}`,
  };
}

export default function App() {
  const [nowMs, setNowMs] = useState(() => Date.now());
  const [arrivalSlot, setArrivalSlot] = useState('11月14日(10点~13点)');
  const [directionsOpen, setDirectionsOpen] = useState(false);
  const [timeModalOpen, setTimeModalOpen] = useState(false);

  useEffect(() => {
    const syncNow = () => {
      setNowMs(Date.now());
    };

    const intervalId = window.setInterval(syncNow, 200);
    const handleVisibility = () => {
      if (document.visibilityState === 'visible') {
        syncNow();
      }
    };

    document.addEventListener('visibilitychange', handleVisibility);
    window.addEventListener('focus', syncNow);

    return () => {
      window.clearInterval(intervalId);
      document.removeEventListener('visibilitychange', handleVisibility);
      window.removeEventListener('focus', syncNow);
    };
  }, []);

  const { dateLabel, timeLabel } = getBeijingClockParts(nowMs);

  return (
    <div className="min-h-dvh w-full bg-[#f3f2ef] sm:py-6 flex justify-center items-stretch sm:items-center">
      <main className="w-full max-w-[430px] min-h-dvh sm:min-h-[820px] bg-white flex flex-col sm:rounded-[24px] sm:overflow-hidden sm:shadow-[0_18px_50px_rgba(0,0,0,0.12)]">
        {/* Top Burgundy Curved Header Banner */}
        <section
          className="relative overflow-hidden px-5 pt-7 pb-10"
          style={{
            background:
              'radial-gradient(circle at 22% 45%, #7b1012 0%, #5e0809 55%, #460405 100%)',
          }}
        >
          {/* Decorative Champagne / Gold Ribbon Swoosh on Right */}
          <svg
            viewBox="0 0 220 180"
            className="absolute right-0 top-0 h-full w-[55%] pointer-events-none select-none"
            preserveAspectRatio="none"
            aria-hidden="true"
          >
            <defs>
              <linearGradient id="ribbonGrad" x1="0%" y1="0%" x2="100%" y2="100%">
                <stop offset="0%" stopColor="#dcb892" stopOpacity="0.88" />
                <stop offset="45%" stopColor="#b98a5f" stopOpacity="0.72" />
                <stop offset="100%" stopColor="#6f3b26" stopOpacity="0.2" />
              </linearGradient>
              <linearGradient id="foldShade" x1="0%" y1="0%" x2="100%" y2="0%">
                <stop offset="0%" stopColor="#3c0203" stopOpacity="0.45" />
                <stop offset="100%" stopColor="#3c0203" stopOpacity="0" />
              </linearGradient>
            </defs>
            {/* Subtle dark burgundy cloth shadow wave */}
            <path
              d="M35,180 C65,125 90,70 115,0 L220,0 L220,180 Z"
              fill="url(#foldShade)"
            />
            {/* Thin curved champagne gold ribbon */}
            <path
              d="M110,180 C138,118 158,64 166,18 C168,8 164,2 152,0 L122,0 C145,5 154,14 150,28 C138,72 115,128 86,180 Z"
              fill="url(#ribbonGrad)"
            />
            <path
              d="M150,180 C166,132 178,86 184,42 C187,18 178,5 158,0 L165,0 C186,6 196,20 193,45 C186,90 174,135 158,180 Z"
              fill="#7c1e16"
              opacity="0.32"
            />
          </svg>

          {/* Header Content */}
          <div className="relative z-10 flex items-center gap-5">
            <BiemlofenLogo />

            <div className="text-white text-[16px] leading-[1.82] tracking-[0.01em] font-normal">
              <div className="flex items-baseline">
                <span>姓名：</span>
                <span className="ml-2">刘淑珍</span>
              </div>
              <div className="flex items-baseline">
                <span>手机：</span>
                <span className="ml-2 tabular-nums">{PASS_PHONE}</span>
              </div>
              <div className="flex items-baseline">
                <span>邀请亲友：</span>
                <span className="ml-2">魏静玉</span>
              </div>
            </div>
          </div>

          {/* Bottom White Arch Edge */}
          <svg
            viewBox="0 0 430 22"
            preserveAspectRatio="none"
            className="absolute bottom-0 left-0 w-full h-[18px] pointer-events-none"
            aria-hidden="true"
          >
            <path d="M0,22 Q215,0 430,22 L430,22 L0,22 Z" fill="#ffffff" />
          </svg>
        </section>

        {/* Address & Navigation Button */}
        <section className="pl-5 pr-3 pt-3.5 pb-6 flex items-start justify-between gap-2 bg-white">
          <div className="flex items-start flex-1 min-w-0 text-[clamp(13.5px,3.7vw,16px)] leading-[1.6] text-[#111111]">
            <span className="shrink-0">地址：</span>
            <div className="min-w-0">
              <div className="whitespace-nowrap">广州市番禺区南村镇兴业大道东</div>
              <div className="whitespace-nowrap">608号比音勒芬服饰股份有限公</div>
              <div className="whitespace-nowrap">司商业办公楼4楼</div>
            </div>
          </div>

          <button
            type="button"
            onClick={() => setDirectionsOpen(true)}
            className="bg-[#191919] hover:bg-black text-white rounded-full pl-3.5 pr-3.5 min-h-[36px] flex items-center gap-2 shrink-0 mt-0.5 active:scale-95 transition-transform cursor-pointer"
          >
            <svg
              viewBox="0 0 20 20"
              className="w-[16px] h-[16px] shrink-0"
              fill="none"
              stroke="currentColor"
              strokeWidth="1.5"
              strokeLinecap="round"
              strokeLinejoin="round"
              aria-hidden="true"
            >
              <path d="M10 13.5C10 13.5 14.5 9.6 14.5 6.8C14.5 4.31 12.49 2.3 10 2.3C7.51 2.3 5.5 4.31 5.5 6.8C5.5 9.6 10 13.5 10 13.5Z" />
              <circle cx="10" cy="6.8" r="1.5" />
              <ellipse cx="10" cy="15.8" rx="4.5" ry="1.6" />
            </svg>
            <span className="text-[14.5px] font-normal whitespace-nowrap">前往</span>
          </button>
        </section>

        {/* Middle Barcode & Live Clock */}
        <section className="flex-1 flex flex-col items-center justify-center px-5 pt-4 pb-8">
          <Barcode value={PASS_PHONE} />

          <div className="mt-10 flex flex-col items-center text-center">
            <div className="text-[15.5px] leading-snug text-[#111111] font-normal tracking-[0.01em]">
              {dateLabel}
            </div>
            <div className="mt-3 text-[44px] sm:text-[46px] leading-none text-[#000000] font-normal tabular-nums tracking-[0.02em]">
              {timeLabel}
            </div>
          </div>
        </section>

        {/* Bottom Terracotta Appointment Confirmation Banner */}
        <footer className="mt-auto bg-[#994129] text-white pl-4 pr-2.5 pt-2 pb-16 flex flex-col">
          <div className="flex justify-end">
            <button
              type="button"
              onClick={() => setTimeModalOpen(true)}
              className="border border-[#e9d2c7]/80 bg-white/[0.04] rounded-full px-2.5 py-1 text-[11.5px] leading-none text-[#f6ece6] hover:bg-white/10 active:scale-95 transition-all whitespace-nowrap cursor-pointer"
            >
              修改到场时间
            </button>
          </div>

          <div className="-mt-0.5 flex flex-col items-center text-center">
            <p className="text-[15.5px] font-normal tracking-[0.02em] text-white/95">
              预约成功！
            </p>
            <p className="mt-1.5 text-[18.5px] sm:text-[19px] font-bold tracking-[0.015em] text-[#f2cf80] tabular-nums">
              {arrivalSlot}
            </p>
          </div>
        </footer>
      </main>

      <DirectionsModal
        isOpen={directionsOpen}
        onClose={() => setDirectionsOpen(false)}
        address={PASS_ADDRESS}
      />

      <TimeSlotModal
        isOpen={timeModalOpen}
        onClose={() => setTimeModalOpen(false)}
        currentSlot={arrivalSlot}
        onSelectSlot={setArrivalSlot}
      />
    </div>
  );
}
