# ergasia
 a = Buffer.read(s,"C:/Users/chris/Desktop/omilia.wav");
b=  Buffer.read(s,"C:/Users/chris/Desktop/output11.wav")
d = Buffer.read(s,"C:/Users/chris/Desktop/output35.wav")
e =  Buffer.read(s,"C:/Users/chris/Desktop/output22.wav")

b.play;
a.play;
d.play;
(
SynthDef("playbuf",{ arg out=0,bufnum=0, rate=2, trigger=1, startPos=0;
var sig,env,source,fx;

	env =  EnvGen.kr(Env([ 0, 5,1,8,4 ], [ 1, 1,1,1, ]));
	source= SinOsc.ar(1300,0.5);
	sig = PlayBuf.ar(1,bufnum, MouseX.kr(1,rate), trigger, BufFrames.ir(bufnum)*startPos);
	fx= DelayC.ar(source, sig);

	sig = sig * env * fx;
	sig = Pan2.ar(sig,MouseX.kr(-1,1), 1);

	Out.ar(out,sig);







}).add;





SynthDef(\playbuf2, {
	arg out=0,bufnum=6, rate=1, trigger=1, startPos=0;
	var sig,env,source,fx;

	env =  EnvGen.kr(Env([ 0, 5,1,8,4 ], [ 1, 1,1,1, ]));
	source= Saw.ar(1000);
	sig = PlayBuf.ar(1,bufnum, MouseX.kr(0.5,rate), trigger, BufFrames.ir(bufnum)*startPos);
	fx= DelayC.ar(source, sig);

	sig = sig * env * fx;
	sig = Pan2.ar(sig,MouseX.kr(-1,1), 1);



	Out.ar(out,sig);
}).add;




SynthDef(\playbuf3, {
	arg out=0,bufnum=13, rate=1, trigger=1, startPos=0;
	var sig,env,source,fx;

	env =  EnvGen.kr(Env([ 0, 6,1,8,2 ], [ 1, 1,1,1, ]));
	source= SinOsc.ar(2000);
	sig = PlayBuf.ar(1,bufnum, MouseX.kr(0.5,rate), trigger, BufFrames.ir(bufnum)*startPos);
	fx= DelayN.ar(source, sig);

	sig = sig * env * fx;
	sig = Pan2.ar(sig,MouseX.kr(-1,1), 1);



	Out.ar(out,sig);
}).add;





SynthDef(\playbuf4,{
	arg out=0,bufnum=14, rate=1, trigger=1, startPos=0,atk=2, sus=0, rel= 5;
	var sig,env,source,del,z;
	env=EnvGen.kr(Env([9,5,8,5],[atk,sus,rel],doneAction:2));
	 z = Dust.ar(1000);
	source = WhiteNoise.ar(Impulse.ar(0.8,0.3,0.5, 0.5, 0));
	sig = PlayBuf.ar(1,bufnum, MouseX.kr(0.1,rate), trigger, BufFrames.ir(bufnum)*startPos);
	del=Delay2.ar(sig+z);
		sig = sig * env * source;


		Out.ar(out,sig);
}).add;


SynthDef(\hip, { arg out=0,freq=200,atk=2,sus=0,amp=0.1,rel=5;
	var sig, env;
	env=EnvGen.kr(Env([0,1,1,5,8,6,5,3,2,0],[atk,sus,rel]),doneAction:20) * Saw.ar(0.5);
	sig=LFPulse.kr(XLine.kr(200, 600, 10), 0, 0.2)*SinOsc.ar();
		sig = sig * env * amp;

    Out.ar(out,
       sig
    );
}).add;


SynthDef(\hop, { arg out=0, freq=900, amp=0.1,release=1,atk=2,sus=0;
	var sig,  gen;
	sig=SinOsc.ar(freq) * Saw.ar(0.1);
	gen =EnvGen.kr(Env([0,1,1,5,8,10,5,3,2,0],[atk,sus,release]));
	Out.ar(out,sig*gen);
}).add;



SynthDef(\sound,{|freq=440,amp=0.1,dur= 0.2,cutoff=500,atk=2,sus=0,rel=3|

var  filter, sig, env;
	env=EnvGen.kr(Env([0,1,1,5,8,10,5,3,2,0],[atk,sus,rel]),doneAction:20);
	sig=LFPulse.kr(XLine.kr(200, 600, 10), 0, 0.2) * Saw.ar(0.5)* WhiteNoise.ar(0.5)*env;






filter= LPF.ar(sig,Line.kr(cutoff,300,dur));



Out.ar(0,filter)

}).add;

)




g= Synth(\sound);
a = Synth(\hop);

(
[40,80,40].midicps.do{
arg f;
Synth(\hip,

		[
			\amp ,0.25 ]
	);
});

(
Synth(\playbuf,
	[\out, 0, \bufnum, a.bufnum,]);
)

(
Synth(\playbuf2,
	[\out,0,\bufnum, b.bufnum]);
)
(
Synth(\playbuf3,[\bufnum, d.bufnum,]);
)
(
Synth(\playbuf4,[\bufnum,e.bufnum]);
)


//edw einai ta idia synths apla exw allaxei ta bufnums sto ka8ena wste o kathe hxos na uiothethsei tis idiothtes toy allou
(
Synth(\playbuf,
	[\out, 0, \bufnum, d.bufnum,]);
)

(
Synth(\playbuf2,
	[\out,0,\bufnum, a.bufnum]);
)
(
Synth(\playbuf3,[\bufnum, e.bufnum,]);
)
(
Synth(\playbuf4,[\bufnum,d.bufnum]);
)



(

var clock = TempoClock(1);

var markovmatrix;

var currentstate=3.rand; //start in one of three states



markovmatrix= [

[0.3,0.2,0.1],

[0.0,0.5,0.5],

[0.3,0.4,0.3]

];


{





	5.do{|i|



		("iteration"+i).postln;



		{



			//pulse beats

			10.do{

				Synth(\sound,[

Pbind(

    \out, 0,
	\bufnum, a.bufnum,
	\dur, Pseq([0.5,0.25,0.25]),				// duration of event in beats
\freq, [48,60,64].at(currentstate).midicps);



			//which probability distribution to use depends on what state we're in right now

			currentstate = [0,0.1,0.2].wchoose(markovmatrix[currentstate]);

						\instrument,  \playbuf3]
);



				1.0.wait;

			};



		}.fork(clock);





		{



			15.do{

				Synth(\playbuf,[

Pbind(

    \out, 0,
						\bufnum,  b.bufnum,
	\dur, Pseq([0.5,0.25,0.25]),				// duration of event in beats


						\instrument, \playbuf2)]
);




				0.25.wait;

			};



			15.do{

				Synth(\hop,[

Pbind(

    \out, 0,
	\bufnum, a.bufnum,
	\dur, Pseq([0.5,0.25,0.25]),				// duration of event in beats


						\instrument, \hop)]
);




				0.2.wait;

			};





		}.fork(clock);



		8.wait;



		clock.tempo = clock.tempo*(5/4);



	};



}.fork(clock);



)

//dokimastiko(eektos kuriou kwdika)
(
Pbind(

    \out, 0,
	\bufnum, a.bufnum,
	\dur, Pseq([0.5,0.25,0.25]),				// duration of event in beats


	\instrument, \playbuf3
).play;

)

