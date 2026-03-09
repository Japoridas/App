import { useState } from "react";

const layers = [
  {
    id: "admin",
    label: "Admin Layer",
    color: "#F97316",
    components: [
      { id: "dashboard", label: "Flag Dashboard", desc: "Create, edit, toggle flags. Set targeting rules & rollout %." },
      { id: "audit", label: "Audit Log", desc: "Full history of flag changes with timestamps & author." },
      { id: "segments", label: "User Segments", desc: "Define cohorts: beta users, region, device OS, app version." },
    ],
  },
  {
    id: "backend",
    label: "Backend Layer",
    color: "#38BDF8",
    components: [
      { id: "api", label: "Flag Config API", desc: "REST/GraphQL endpoint. Returns flag state per user context." },
      { id: "rules", label: "Rules Engine", desc: "Evaluates targeting rules: user ID, %, segment, killswitch." },
      { id: "cache", label: "Config Cache (Redis)", desc: "TTL-based cache. Serves flags in <5ms without DB hits." },
      { id: "db", label: "Flag Store (DB)", desc: "Source of truth. Stores flags, rules, variants, metadata." },
    ],
  },
  {
    id: "delivery",
    label: "Delivery Layer",
    color: "#A78BFA",
    components: [
      { id: "sdk", label: "Mobile SDK", desc: "Lightweight client. Fetches & caches flags locally on device." },
      { id: "localcache", label: "Local Cache", desc: "On-device persistence. Works offline with last-known values." },
      { id: "sse", label: "SSE / WebSocket", desc: "Real-time push for flag changes without polling." },
    ],
  },
  {
    id: "app",
    label: "Mobile App Layer",
    color: "#34D399",
    components: [
      { id: "feature", label: "Feature Gate", desc: "if (flags.isEnabled('new_checkout')) { ... } in your code." },
      { id: "ab", label: "A/B Variant", desc: "Assign variant A/B/C per user. Track per-variant metrics." },
      { id: "analytics", label: "Analytics Hook", desc: "Auto-log flag exposure events to your analytics pipeline." },
    ],
  },
];

const flows = [
  { from: "dashboard", to: "db", label: "writes flag config" },
  { from: "db", to: "cache", label: "syncs on write" },
  { from: "cache", to: "api", label: "serves from cache" },
  { from: "api", to: "sdk", label: "returns flag payload" },
  { from: "sdk", to: "localcache", label: "persists locally" },
  { from: "sdk", to: "feature", label: "exposes flag state" },
  { from: "sdk", to: "ab", label: "assigns variant" },
  { from: "feature", to: "analytics", label: "logs exposure" },
  { from: "ab", to: "analytics", label: "logs variant" },
  { from: "sse", to: "sdk", label: "pushes updates" },
  { from: "rules", to: "api", label: "evaluates rules" },
  { from: "segments", to: "rules", label: "segment definitions" },
  { from: "audit", to: "db", label: "reads history" },
];

export default function FeatureFlagDiagram() {
  const [active, setActive] = useState(null);
  const [hoveredLayer, setHoveredLayer] = useState(null);

  const allComponents = layers.flatMap(l =>
    l.components.map(c => ({ ...c, layerColor: l.color, layerId: l.id }))
  );
  const activeComp = allComponents.find(c => c.id === active);
  const activeLayer = active ? layers.find(l => l.id === activeComp?.layerId) : null;

  const relatedIds = active
    ? flows.filter(f => f.from === active || f.to === active).flatMap(f => [f.from, f.to])
    : [];

  return (
    <div style={{
      minHeight: "100vh",
      background: "#0A0A0F",
      fontFamily: "'DM Mono', 'Fira Code', monospace",
      color: "#E2E8F0",
      padding: "32px 24px",
    }}>
      <link href="https://fonts.googleapis.com/css2?family=DM+Mono:wght@300;400;500&family=Syne:wght@700;800&display=swap" rel="stylesheet" />
      <div style={{ marginBottom: 36, borderBottom: "1px solid #1E293B", paddingBottom: 20 }}>
        <div style={{ display: "flex", alignItems: "baseline", gap: 12, marginBottom: 6 }}>
          <span style={{ fontFamily: "'Syne', sans-serif", fontSize: 26, fontWeight: 800, letterSpacing: "-0.5px", color: "#F8FAFC" }}>
            Feature Flag System
          </span>
          <span style={{ fontSize: 11, color: "#64748B", background: "#1E293B", padding: "2px 8px", borderRadius: 4 }}>MOBILE APP</span>
        </div>
        <p style={{ fontSize: 12, color: "#64748B", margin: 0 }}>
          Click any component to explore its role and connections →
        </p>
      </div>

      <div style={{ display: "flex", gap: 24, alignItems: "flex-start", flexWrap: "wrap" }}>
        <div style={{ flex: "1 1 480px" }}>
          {layers.map((layer, li) => (
            <div key={layer.id} style={{ marginBottom: 16 }}>
              <div style={{
                display: "flex", alignItems: "center", gap: 10, marginBottom: 8,
                opacity: hoveredLayer && hoveredLayer !== layer.id ? 0.4 : 1,
                transition: "opacity 0.2s",
              }}>
                <div style={{ width: 10, height: 10, borderRadius: "50%", background: layer.color, boxShadow: `0 0 8px ${layer.color}88` }} />
                <span style={{ fontSize: 10, fontWeight: 500, color: layer.color, letterSpacing: "0.12em", textTransform: "uppercase" }}>
                  {layer.label}
                </span>
                <div style={{ flex: 1, height: "1px", background: `${layer.color}33` }} />
              </div>

              <div style={{ display: "flex", gap: 8, flexWrap: "wrap", paddingLeft: 20 }}>
                {layer.components.map(comp => {
                  const isActive = active === comp.id;
                  const isRelated = relatedIds.includes(comp.id) && !isActive;
                  const isDimmed = active && !isActive && !isRelated;
                  return (
                    <button
                      key={comp.id}
                      onClick={() => setActive(isActive ? null : comp.id)}
                      onMouseEnter={() => setHoveredLayer(layer.id)}
                      onMouseLeave={() => setHoveredLayer(null)}
                      style={{
                        background: isActive ? layer.color : isRelated ? `${layer.color}22` : "#111827",
                        border: `1px solid ${isActive ? layer.color : isRelated ? layer.color : "#1E293B"}`,
                        borderRadius: 6,
                        padding: "8px 14px",
                        color: isActive ? "#0A0A0F" : isRelated ? layer.color : "#94A3B8",
                        fontSize: 11,
                        fontFamily: "inherit",
                        fontWeight: isActive ? 600 : 400,
                        cursor: "pointer",
                        opacity: isDimmed ? 0.25 : 1,
                        transition: "all 0.18s ease",
                        boxShadow: isActive ? `0 0 16px ${layer.color}66` : isRelated ? `0 0 8px ${layer.color}33` : "none",
                        transform: isActive ? "translateY(-1px)" : "none",
                        letterSpacing: "0.02em",
                      }}
                    >
                      {comp.label}
                    </button>
                  );
                })}
              </div>

              {li < layers.length - 1 && (
                <div style={{ display: "flex", justifyContent: "center", padding: "8px 0", opacity: 0.4 }}>
                  <div style={{ display: "flex", flexDirection: "column", alignItems: "center", gap: 2 }}>
                    <div style={{ width: 1, height: 12, background: "#334155" }} />
                    <svg width="8" height="6" viewBox="0 0 8 6">
                      <path d="M4 6 L0 0 L8 0 Z" fill="#334155" />
                    </svg>
                  </div>
                </div>
              )}
            </div>
          ))}
        </div>

        <div style={{
          width: 260, flexShrink: 0, background: "#0F172A",
          border: `1px solid ${activeComp ? activeLayer?.color + "55" : "#1E293B"}`,
          borderRadius: 10, padding: 20, transition: "border-color 0.2s", minHeight: 200,
        }}>
          {activeComp ? (
            <>
              <div style={{ display: "flex", alignItems: "center", gap: 8, marginBottom: 14 }}>
                <div style={{ width: 8, height: 8, borderRadius: "50%", background: activeLayer?.color, boxShadow: `0 0 8px ${activeLayer?.color}` }} />
                <span style={{ fontSize: 10, color: activeLayer?.color, textTransform: "uppercase", letterSpacing: "0.1em" }}>
                  {activeLayer?.label}
                </span>
              </div>
              <div style={{ fontFamily: "'Syne', sans-serif", fontSize: 16, fontWeight: 700, color: "#F8FAFC", marginBottom: 10 }}>
                {activeComp.label}
              </div>
              <p style={{ fontSize: 12, color: "#94A3B8", lineHeight: 1.7, marginBottom: 18 }}>{activeComp.desc}</p>
              <div style={{ fontSize: 10, color: "#475569", textTransform: "uppercase", letterSpacing: "0.1em", marginBottom: 10 }}>Connections</div>
              {flows.filter(f => f.from === active || f.to === active).map((f, i) => {
                const isOut = f.from === active;
                const otherId = isOut ? f.to : f.from;
                const otherComp = allComponents.find(c => c.id === otherId);
                return (
                  <div key={i} style={{ display: "flex", alignItems: "flex-start", gap: 8, marginBottom: 8, fontSize: 11, color: "#64748B" }}>
                    <span style={{ color: isOut ? "#F97316" : "#38BDF8", fontSize: 13, lineHeight: 1.2 }}>{isOut ? "→" : "←"}</span>
                    <div>
                      <span style={{ color: "#94A3B8" }}>{otherComp?.label}</span>
                      <br />
                      <span style={{ fontSize: 10, color: "#475569" }}>{f.label}</span>
                    </div>
                  </div>
                );
              })}
            </>
          ) : (
            <div style={{ display: "flex", flexDirection: "column", justifyContent: "center", alignItems: "center", height: 160, gap: 10 }}>
              <div style={{ fontSize: 28, opacity: 0.2 }}>⬡</div>
              <span style={{ fontSize: 11, color: "#334155", textAlign: "center", lineHeight: 1.6 }}>
                Select a component<br />to see its role & connections
              </span>
            </div>
          )}
        </div>
      </div>

      <div style={{ marginTop: 32, paddingTop: 20, borderTop: "1px solid #1E293B", display: "flex", gap: 24, flexWrap: "wrap" }}>
        {layers.map(l => (
          <div key={l.id} style={{ display: "flex", alignItems: "center", gap: 8 }}>
            <div style={{ width: 8, height: 8, borderRadius: "50%", background: l.color }} />
            <span style={{ fontSize: 11, color: "#64748B" }}>{l.label}</span>
          </div>
        ))}
        <div style={{ marginLeft: "auto", fontSize: 11, color: "#334155" }}>
          <span style={{ color: "#F97316" }}>→</span> outgoing &nbsp;
          <span style={{ color: "#38BDF8" }}>←</span> incoming
        </div>
      </div>
    </div>
  );
}
