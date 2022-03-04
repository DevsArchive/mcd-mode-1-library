# Mega CD Mode 1 Library
A small library for working with booting mode 1 for the Mega CD.

## What is "Mode 1"?
Simply just booting from cartridge while the Mega CD is attached to the Mega Drive. There is a pin on the cartridge port that dictates how to arrange memory for the cartridge and expansion. Cartridge games will have cartridge memory be the first thing visible to the 68000, with expansion memory placed after. If a cartridge game is not present, then the memory is arranged the other way around. This also means that expansion memory IS still visible, and we CAN make use of it.

## How do I use this?
All you really need to do to get started is, after game initialization, call FindMCDBIOS to detect the BIOS. If the game finds a known BIOS, then you can call InitSubCPU to initialize the Sub CPU and upload your initial system program. After that, you can then wait for the Sub CPU to boot and initialize. For that to work, however, you need to enable vertical interrupts, and have your handler call FireMCDIRQ2 or however you choose to fire an IRQ2 request to the Sub CPU. You can then choose to disable interrupts and stop firing IRQ2 requests after the Sub CPU is properly initiaized.

		jsr	FindMCDBIOS			; Find the MCD's BIOS
		bcs.s	@Skip				; If it wasn't found, branc

		lea	SubProgram(pc),a1		; Initialize Sub CPU
		move.w	#SubProgramSize-1,d0
		jsr	InitSubCPU

		move	#$2300,sr			; Enable interrupts
		; Wait for the Sub CPU to initialize here

	@Skip:
		...

	VInterrupt:
		jsr	FireMCDIRQ2			; Fire MCD IRQ2
		...
		
Sub CPU BIOS code decompression also requires the [Kosinski Decompression routine](https://segaretro.org/Kosinski_compression#Decompression_code) (called KosDec here).
