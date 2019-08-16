# ConorWalshCsound
Csound Code for Ibanez Tube Screamer Emulation
<Cabbage>
form caption("Clipper") size(400, 400), colour(30, 30, 30), pluginid("def1")
rslider bounds(290, 6, 102, 82), channel("gain"), range(0, 1, 0.2, 1, .01), text("Gain")
 51
Appendices
 groupbox bounds(2, 2, 93, 122), text("Overdrive")
groupbox bounds(96, 2, 180, 122), text("Tanh waveshaping")
checkbox bounds(290, 86, 100, 24), text("original"), channel("original")
rslider bounds(22, 32, 60, 60) range(0, 500, 1, 1, 0.001), channel("drive"), text("Drive")
checkbox bounds(6, 100, 85, 20), text("Clipping"), channel("enableClip"), radiogroup(99), value(1)
rslider bounds(158, 32, 60, 60) range(0, 400, 200, .5, 0.001), channel("tanhAmount"), text("Tanh") checkbox bounds(132, 96, 111, 25), text("Enable Tanh"), channel("enableTanh"), radiogroup(99), value(0) rslider bounds(294, 112, 93, 77) range(0, 5000, 5000, .5, 0.001), text("tone"), channel("lowpass")
 gentable bounds(4, 126, 273, 169), tablenumber(1), amprange(-1, 1, 1), identchannel("tableIdent"), fill(0), outlinethickness(10)
combobox bounds(284, 246, 100, 25), populate("*.snaps")
filebutton bounds(304, 218, 60, 25), text("Save", "Save"), populate("*.snaps"), mode("snapshot") value(0)
</Cabbage> <CsoundSynthesizer> <CsOptions>
-n -d -+rtmidi=NULL -M0 -m0d </CsOptions> <CsInstruments>
; Initialize the global variables.
ksmps = 32
 nchnls = 2 0dbfs = 1
instr 1
aMix init 0
if chnget:k("original") == 1 then
a1, a2 diskin2 "Distortion.wav", 1, 0, 1 aMix = a1
;orignal signal - overdriven
 else ;clean signal to be processed a1, a2 diskin2 "Clean.wav", 1, 0, 1
aHp butterhp a1, 720
aLp butterlp a1, 720
if chnget:k("enableTanh")==1 then ;enable tanh waveshaping
 aDist tablei (aHp+1)/2, 1, 1 kScl tablei 1, 2, 1
aDist = aDist*kScl
 else ;do hardclipping
 52

Appendices
 a1 = a1*chnget:k("drive")
aDist limit a1, -1, 1 endif
aDist tone aDist, chnget:k("lowpass")
aMix = (aDist+aLp)*chnget:k("gain")*.5 endif
outs aMix, aMix
;apply lowpass filter range 0-5000
;output signal
; fout "recording.wav", 4, aMix, aMix
kTanhAmount chnget "tanhAmount" if changed(kTanhAmount) == 1 then
event "i", 10, 0, 0, kTanhAmount endif
endin
;update tanh transfer function
instr 10
giTransfer ftgen 1, 0, 4097, "tanh", -p4, p4 chnset "tablenumber(1)", "tableIdent"
endin
;update tanh table if slider tanh moves
</CsInstruments>
<CsScore>
f1 0 4097 "tanh" -50, 50
f2 0 1024 4 1 1
;causes Csound to run for about 7000 years... i10 0 z
f0 z
;starts instrument 1 and runs it for a week
i1 0 z
</CsScore> </CsoundSynthesizer>
