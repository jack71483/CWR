# CWR
CWR_Common_Work_Registration



from PyFileMaker import FMServer
from grp import groupby
import re
fm = FMServer("Admin:JD3212@192.168.1.116:491", "catalyst 2.0", "CWR") # user:password@host, db name, layout name
allFields = fm.doView()
allRecords = fm.doFindAll()
output = open("Z:\CWR\Output.txt", "w")
recordCount = 1
songCount = 0
position = 0

	
	def __init__(self, songNumber):
		self.songNumber = songNumber
		self.currentRecordLength = songRecordLength[songNumber]
		self.end = position + self.currentRecordLength
		self.one = position + songRecordLength[0] - 1
		self.recordCount = 0 
		self.sequenceCount = 0
		self.sequenceCount1 = 0
		self.sequenceCount2 = 0
		self.clients = [(p) for p in range(position, self.end) if (allRecords[p]['clientID'])]
		self.other = [(p) for p in range(position, self.end) if (allRecords[p]['OfirstName'])]
		self.songID = getRecordDetail(position,'songID')
		self.territoryID = getRecordDetail(position,'territory')
		self.writeSong()
	
	def endRecord(self, static=0):
		global recordCount
		output.write("\n")
		recordCount += 1

	def resetCount(self):
		self.sequenceCount2 = 1
		self.sequenceCount = 1
		
	def addCount(self):
		self.sequenceCount += 1
	

	def addCount2(self):
		self.sequenceCount2 += 1

	def updatePosition(self):
		global position
		position += self.currentRecordLength
		
	def writeSong(self):
		self.NWR(position)
		self.interestedParty = []
		for p in self.clients:
			self.interestedParty.append(self.SPU(p, 1))
			self.interestedParty.append(self.SPU(p, 0))
			self.interestedParty.append(self.SPU1(p, 0))
			self.interestedParty.append(self.SPT(p))
			self.interestedParty.append(self.SPT1(p))
			self.interestedParty.append(self.SPU2(p, 0))
			self.interestedParty.append(self.SPT2(p))
			self.interestedParty.append(self.SPU3(p, 0))
			self.addCount2()
			self.interestedParty.append(self.SPT3(p))
		self.otherpub =[]
		for p in self.other:
			self.otherpub.append(self.OPU(p))
			self.addCount2()
		self.composers = []
		for p in self.clients:
			self.interestedParty.append(self.SWR(p))
			self.addCount2()
			self.interestedParty.append(self.SWT(p, 0))
			self.interestedParty.append(self.PWR(p))
		self.otherwriter = [self.OWR(p) for p in self.other]
		self.Artist = [self.PER(p) for p in range(position, self.one)]
		self.AltTitle = [self.ALT(p) for p in range(position, self.one)]
		self.updatePosition()

	def NWR(self, recordNumber): #page 24
		process(0,3,'NWR') #NWR=new registration, REV=Revised registration or ISW=notification of ISWC or EXC=Existing work in conflict
		process(3,8, str(self.songNumber),0,1)
		process(11,8, str(self.sequenceCount),0,1)
		process(19,60,getRecordDetail(recordNumber,'songTitle'),1) #work Title.
		process(79,2,'EN') #language code - represent the language of this title *see language code table.
		process(81,14,getRecordDetail(recordNumber,'songID'),0,1)
		# *note* submitter work number - assigned to the work by the publisher submitting or receiving the file. must be unique for the publisher
		process(95,11,'',1) #ISWC - international standard work code assigned to this work. ???
		process(106,8,'',1) #copyright date - origional copyright date of the work
		process(114,12,'',1) #copyright number - origional copyright number of this work."""
		process(126,3, 'POP') #Musical Work Distribution Catagory - value for this field reside in the musical work distribution categry table.
		process(129,6) #Duration - Duration of the work in hours, minutes, and secnds. must be greater then zero if musical work distribution category is equal to SER. some societies may also require duration for works where the musical work distribution category is equal to JAZ. 
		process(135,1,'U') #recorded indicator - indicates whether or not the work has ever been recorded.
		process(136,3,'',1) #text music relationship - whether this work contains music, text, and/or both.
		process(139,3,'',1) #composite type - if this is a composite work, this field will indicate the type of composite. values reside in the composite type table.
		process(142,3, 'ORI') #version type - this field is used to indicate whether or not this work is an arrangement. values reside in the version type table. 
		process(145,3,'',1) #excerpt type - this is an excerpt this field will indicate the type of excerpt values reside in the except type table.
		process(148,3,'',1) #music arrangement - if version type is equal to "MOD", this field indicates the type of music arrangement. Values reside in the music arrangement table. 	
		process(151,3,'',1) #Lyric adaptation - if version type is equal to "MOD", this field indicates the type of music arrangement. Value reside in the Lyric Adaptation Table. 
		process(154,30, 'JACK HSU',1) #contact name - name of a business contact person at the organization that originated this transaction.
		process(184,10,'',1) #contact ID - identifier associated with the contact person at the organization that originated this transaction.
		process(194,2,'',1) #CWR Work Type - these calues reside in the CWR work type table.
		process(196,1,'N',1) #grand rights ind - indicates whether this work is originally intended or performance on stages. *this field is mandatory for registration with the UK societies.
		process(197,3,'',1) #composite component count - if coposite type is entered, this field represents the number of components contained in this composite. *that this is require for composite work where ASCAP is represented on the work. 
		process(200,8,'',1) #Date of Publiscation of printed edition.
		process(208,1,'N',1) # *note* exceptional clause - registrations with GEMA: by entering Y (Yes), the submitting GEMA-sub-pubilsher declares that the exceptional clause of the GEMA distribution rules with regad to printed editions applies.
		self.resetCount()
		self.endRecord()

	def SPU(self, recordNumber, publisher=0):     #This defaults to composer.  If record is a publisher record, publisher = 1
		process(0,3,'SPU') #publisher contoll by submitter size: 19
		process(3,8, str(self.songNumber),0,1)
		process(11,8, str(self.sequenceCount),0,1) #publisher controll by sumitter size 19 sequence count from 01
		process(19,2, str(self.sequenceCount2),0,1) #publisher sequence number

		(publisher and [process(21,9,getRecordDetail(recordNumber, 'clientID'),0,1)] or [process(21,
						9,getRecordDetail(recordNumber, 'clientID'),0,1)])[0] #interested party number.
		(publisher and [process(30,45, getRecordDetail(recordNumber, 'publisherName'),1)] or [process(30,45,getRecordDetail(recordNumber, 'MLMPubName'),1)])[0] #publisher name.
		process(75,1,'',1)  #publisher unknown indicator, indicates if the name of this pulisher is unknown. note that this field must be left blank for SPU records.		
		(publisher and [process(76,2,'E',1)] or [process(76,2,'ES')])[0] #publisher type, code defining this publisher's role in the publishing of the work. values reside on the publisher type table. required for record type SPU and option for record type OPU.
		process(78,9,'',1) #number use to identify this publisher for domestic tax reporting.
		process(87,2)
		(publisher and [process(89,9,getRecordDetail(recordNumber, 'publisherCAE'),1)] or 
					   [process(89,9,getRecordDetail(recordNumber, 'MLMPublisherCAE'),1)])[0] #publisher IPI.
		process(98,14,'',1) #submitter agreement number - indicates the agreement number unique to the submitter uner which this publisher has acquired the rights to this work.
		process(112,3, getRecordDetail(recordNumber, 'publisherSocietyCWR')[:3],1) #PR Affliation society number - number assigned to the mechanical rights society with which the publisher is affiliated. these values erside on the society code table.
		(publisher and [process(115,5, getRecordDetail(recordNumber, 'OOperfOwned'),0,1)] or [process(115,5,getRecordDetail(recordNumber,'pubmech'),0,1)])[0] #PR Ownership share - the percentage of publisher ownership of the performance right to the work.
		process(120,3, getRecordDetail(recordNumber, 'publisherSocietyCWR')[:3],1) #MR Society - number assigned to the mechanical rights society with which the publisher is affiliated. under society coe table.
		(publisher and [process(123,5, getRecordDetail(recordNumber, 'OOmechOwned'),0,1)] or [process(123,5,getRecordDetail(recordNumber,'pubmech'),0,1)])[0]  #MR Ownership share - percentage of the publisher's ownership of the mechanical rights to the work. * does not define the percentage of the total royalty distributed tfor sale of CD, Cassettes etc. containing the work that will be collected by the publisher. 
		process(128,3, getRecordDetail(recordNumber, 'publisherSocietyCWR')[:3],1) #SR society - number assigned to the society with which the publisher is affiliated for admin of sync rihts. * see uder society code table.
		(publisher and [process(131,5, getRecordDetail(recordNumber, 'OOmechOwned'),0,1)] or [process(131,5,getRecordDetail(recordNumber,'pubmech'),0,1)])[0]  #SR ownership share - percentage of the publisher's ownership of synch right tot he work. share does not define the percentage of the total money distributed that will be collected by the publisher. within an individual SPU record, this value can range from 0 to 100. 
		process(136,1,'',1) #speicial agreements indicator - publisher claiming reversionary rights.*this flag only applies to societies that reognize reversionary rights. for example SOCAN. 
		process(137,1,'N') #fire recording refusal ind - indicates whether the sumitter has refused to give authority for the first recording. *that this field is mandatory for registration with the UK societies.
		process(138,1,'',1) #filler - fill with a blank.
		self.addCount()
		self.endRecord()

	def SPU1(self, recordNumber, publisher=0):     #This defaults to composer.  If record is a publisher record, publisher = 1
		process(0,3,'SPU') #publisher contoll by submitter size: 19
		process(3,8, str(self.songNumber),0,1)
		process(11,8, str(self.sequenceCount),0,1) #publisher controll by sumitter size 19 sequence count from 01
		process(19,2, str(self.sequenceCount2),0,1) #publisher sequence number

		(publisher and [process(21,9,getRecordDetail(recordNumber, 'clientID'),0,1)] or [process(21,
						9,getRecordDetail(recordNumber, 'clientID'),0,1)])[0] #interested party number.
		(publisher and [process(30,45, getRecordDetail(recordNumber, 'publisherName'),1)] or [process(30,45,getRecordDetail(recordNumber, 'STIMPubName'),1)])[0] #publisher name.
		process(75,1,'',1)  #publisher unknown indicator, indicates if the name of this pulisher is unknown. note that this field must be left blank for SPU records.		
		(publisher and [process(76,2,'E',1)] or [process(76,2,'ES')])[0] #publisher type, code defining this publisher's role in the publishing of the work. values reside on the publisher type table. required for record type SPU and option for record type OPU.
		process(78,9,'',1) #number use to identify this publisher for domestic tax reporting.
		process(87,2)
		(publisher and [process(89,9,getRecordDetail(recordNumber, 'publisherCAE'),1)] or 
					   [process(89,9,getRecordDetail(recordNumber, 'STIMPublisherCAE'),1)])[0] #publisher IPI.
		process(98,14,'',1) #submitter agreement number - indicates the agreement number unique to the submitter uner which this publisher has acquired the rights to this work.
		process(112,3, getRecordDetail(recordNumber, 'PubSocietySTIM')[:3],1) #PR Affliation society number - number assigned to the mechanical rights society with which the publisher is affiliated. these values erside on the society code table.
		(publisher and [process(115,5, getRecordDetail(recordNumber, 'OOperfOwned'),0,1)] or [process(115,5,getRecordDetail(recordNumber,'pubmech'),0,1)])[0] #PR Ownership share - the percentage of publisher ownership of the performance right to the work.
		process(120,3, getRecordDetail(recordNumber, 'PubSocietySTIM')[:3],1) #MR Society - number assigned to the mechanical rights society with which the publisher is affiliated. under society coe table.
		(publisher and [process(123,5, getRecordDetail(recordNumber, 'OOmechOwned'),0,1)] or [process(123,5,getRecordDetail(recordNumber,'pubmech'),0,1)])[0]  #MR Ownership share - percentage of the publisher's ownership of the mechanical rights to the work. * does not define the percentage of the total royalty distributed tfor sale of CD, Cassettes etc. containing the work that will be collected by the publisher. 
		process(128,3, getRecordDetail(recordNumber, 'PubSocietySTIM')[:3],1) #SR society - number assigned to the society with which the publisher is affiliated for admin of sync rihts. * see uder society code table.
		(publisher and [process(131,5, getRecordDetail(recordNumber, 'OOmechOwned'),0,1)] or [process(131,5,getRecordDetail(recordNumber,'pubmech'),0,1)])[0]  #SR ownership share - percentage of the publisher's ownership of synch right tot he work. share does not define the percentage of the total money distributed that will be collected by the publisher. within an individual SPU record, this value can range from 0 to 100. 
		process(136,1,'',1) #speicial agreements indicator - publisher claiming reversionary rights.*this flag only applies to societies that reognize reversionary rights. for example SOCAN. 
		process(137,1,'N') #fire recording refusal ind - indicates whether the sumitter has refused to give authority for the first recording. *that this field is mandatory for registration with the UK societies.
		process(138,1,'',1) #filler - fill with a blank.
		process(139,13,'',1) #The IPI Base Number assigned to this publisher
		process(152,14,'',1) #The ISAC that has been assigned to the agreement under which the publisher share is to  be administered
		process(166,14,getRecordDetail(recordNumber, 'PRSAgreementNumber'),0,1) #the agreement assigned by the society of the sub publisher
		self.addCount()
		self.endRecord()

	def SPU2(self, recordNumber, publisher=0):     #This defaults to composer.  If record is a publisher record, publisher = 1
		process(0,3,'SPU') #publisher contoll by submitter size: 19
		process(3,8, str(self.songNumber),0,1)
		process(11,8, str(self.sequenceCount),0,1) #publisher controll by sumitter size 19 sequence count from 01
		process(19,2, str(self.sequenceCount2),0,1) #publisher sequence number

		(publisher and [process(21,9,getRecordDetail(recordNumber, 'clientID'),0,1)] or [process(21,
						9,getRecordDetail(recordNumber, 'clientID'),0,1)])[0] #interested party number.
		(publisher and [process(30,45, getRecordDetail(recordNumber, 'publisherName'),1)] or [process(30,45,getRecordDetail(recordNumber, 'UKPubName'),1)])[0] #publisher name.
		process(75,1,'',1)  #publisher unknown indicator, indicates if the name of this pulisher is unknown. note that this field must be left blank for SPU records.		
		(publisher and [process(76,2,'E',1)] or [process(76,2,'ES')])[0] #publisher type, code defining this publisher's role in the publishing of the work. values reside on the publisher type table. required for record type SPU and option for record type OPU.
		process(78,9,'',1) #number use to identify this publisher for domestic tax reporting.
		process(87,2)
		(publisher and [process(89,9,getRecordDetail(recordNumber, 'publisherCAE'),1)] or 
					   [process(89,9,getRecordDetail(recordNumber, 'UKPublisherCAE'),1)])[0] #publisher IPI.
		process(98,14,'',1) #submitter agreement number - indicates the agreement number unique to the submitter uner which this publisher has acquired the rights to this work.
		process(112,3, getRecordDetail(recordNumber, 'PubSocietyUK')[:3],1) #PR Affliation society number - number assigned to the mechanical rights society with which the publisher is affiliated. these values erside on the society code table.
		(publisher and [process(115,5, getRecordDetail(recordNumber, 'OOperfOwned'),0,1)] or [process(115,5,getRecordDetail(recordNumber,'pubmech'),0,1)])[0] #PR Ownership share - the percentage of publisher ownership of the performance right to the work.
		process(120,3, getRecordDetail(recordNumber, 'PubSocietyUK')[:3],1) #MR Society - number assigned to the mechanical rights society with which the publisher is affiliated. under society coe table.
		(publisher and [process(123,5, getRecordDetail(recordNumber, 'OOmechOwned'),0,1)] or [process(123,5,getRecordDetail(recordNumber,'pubmech'),0,1)])[0]  #MR Ownership share - percentage of the publisher's ownership of the mechanical rights to the work. * does not define the percentage of the total royalty distributed tfor sale of CD, Cassettes etc. containing the work that will be collected by the publisher. 
		process(128,3, getRecordDetail(recordNumber, 'PubSocietyUK')[:3],1) #SR society - number assigned to the society with which the publisher is affiliated for admin of sync rihts. * see uder society code table.
		(publisher and [process(131,5, getRecordDetail(recordNumber, 'OOmechOwned'),0,1)] or [process(131,5,getRecordDetail(recordNumber,'pubmech'),0,1)])[0]  #SR ownership share - percentage of the publisher's ownership of synch right tot he work. share does not define the percentage of the total money distributed that will be collected by the publisher. within an individual SPU record, this value can range from 0 to 100. 
		process(136,1,'',1) #speicial agreements indicator - publisher claiming reversionary rights.*this flag only applies to societies that reognize reversionary rights. for example SOCAN. 
		process(137,1,'N') #fire recording refusal ind - indicates whether the sumitter has refused to give authority for the first recording. *that this field is mandatory for registration with the UK societies.
		process(138,1,'',1) #filler - fill with a blank.
		process(139,13,'',1) #The IPI Base Number assigned to this publisher
		process(152,14,'',1) #The ISAC that has been assigned to the agreement under which the publisher share is to  be administered
		process(166,14,getRecordDetail(recordNumber, 'PRSAgreementNumber'),0,1) #the agreement assigned by the society of the sub publisher
		self.addCount()
		self.endRecord()

	def SPU3(self, recordNumber, publisher=0):     #This defaults to composer.  If record is a publisher record, publisher = 1
		process(0,3,'SPU') #publisher contoll by submitter size: 19
		process(3,8, str(self.songNumber),0,1)
		process(11,8, str(self.sequenceCount),0,1) #publisher controll by sumitter size 19 sequence count from 01
		process(19,2, str(self.sequenceCount2),0,1) #publisher sequence number

		(publisher and [process(21,9,getRecordDetail(recordNumber, 'clientID'),0,1)] or [process(21,
						9,getRecordDetail(recordNumber, 'clientID'),0,1)])[0] #interested party number.
		(publisher and [process(30,45, getRecordDetail(recordNumber, 'publisherName'),1)] or [process(30,45,getRecordDetail(recordNumber, 'BUMAPubName'),1)])[0] #publisher name.
		process(75,1,'',1)  #publisher unknown indicator, indicates if the name of this pulisher is unknown. note that this field must be left blank for SPU records.		
		(publisher and [process(76,2,'E',1)] or [process(76,2,'ES')])[0] #publisher type, code defining this publisher's role in the publishing of the work. values reside on the publisher type table. required for record type SPU and option for record type OPU.
		process(78,9,'',1) #number use to identify this publisher for domestic tax reporting.
		process(87,2)
		(publisher and [process(89,9,getRecordDetail(recordNumber, 'publisherCAE'),1)] or 
					   [process(89,9,getRecordDetail(recordNumber, 'BUMAPublisherCAE'),1)])[0] #publisher IPI.
		process(98,14,'',1) #submitter agreement number - indicates the agreement number unique to the submitter uner which this publisher has acquired the rights to this work.
		process(112,3, getRecordDetail(recordNumber, 'PubSocietyBUMA')[:3],1) #PR Affliation society number - number assigned to the mechanical rights society with which the publisher is affiliated. these values erside on the society code table.
		(publisher and [process(115,5, getRecordDetail(recordNumber, 'OOperfOwned'),0,1)] or [process(115,5,getRecordDetail(recordNumber,'pubmech'),0,1)])[0] #PR Ownership share - the percentage of publisher ownership of the performance right to the work.
		process(120,3, getRecordDetail(recordNumber, 'PubSocietyBUMA')[:3],1) #MR Society - number assigned to the mechanical rights society with which the publisher is affiliated. under society coe table.
		(publisher and [process(123,5, getRecordDetail(recordNumber, 'OOmechOwned'),0,1)] or [process(123,5,getRecordDetail(recordNumber,'pubmech'),0,1)])[0]  #MR Ownership share - percentage of the publisher's ownership of the mechanical rights to the work. * does not define the percentage of the total royalty distributed tfor sale of CD, Cassettes etc. containing the work that will be collected by the publisher. 
		process(128,3, getRecordDetail(recordNumber, 'PubSocietyBUMA')[:3],1) #SR society - number assigned to the society with which the publisher is affiliated for admin of sync rihts. * see uder society code table.
		(publisher and [process(131,5, getRecordDetail(recordNumber, 'OOmechOwned'),0,1)] or [process(131,5,getRecordDetail(recordNumber,'pubmech'),0,1)])[0]  #SR ownership share - percentage of the publisher's ownership of synch right tot he work. share does not define the percentage of the total money distributed that will be collected by the publisher. within an individual SPU record, this value can range from 0 to 100. 
		process(136,1,'',1) #speicial agreements indicator - publisher claiming reversionary rights.*this flag only applies to societies that reognize reversionary rights. for example SOCAN. 
		process(137,1,'N') #fire recording refusal ind - indicates whether the sumitter has refused to give authority for the first recording. *that this field is mandatory for registration with the UK societies.
		process(138,1,'',1) #filler - fill with a blank.
		process(139,13,'',1) #The IPI Base Number assigned to this publisher
		process(152,14,'',1) #The ISAC that has been assigned to the agreement under which the publisher share is to  be administered
		process(166,14,getRecordDetail(recordNumber, 'PRSAgreementNumber'),0,1) #the agreement assigned by the society of the sub publisher
		self.addCount()
		self.endRecord()

	def SPT(self, recordNumber, publisher=0): #This is a duplicate of interested party, but calculates the sum of all controlled writers
		sumMech =sum([floatSafe(getRecordDetail(p, 'mechOwned')) for p in range(position, self.end) if getRecordDetail(p, 'controlled') == 'Y'])
		sumPerf =sum([floatSafe(getRecordDetail(p, 'perfOwned')) for p in range(position, self.end) if getRecordDetail(p, 'controlled') == 'Y'])
		process(0,3,'SPT') #record prefix - set record type = SPT (Publisher Territory of Control)
		process(3,8, str(self.songNumber),0,1)
		process(11,8, str(self.sequenceCount),0,1)
		process(19,9,getRecordDetail(recordNumber, 'clientID'),0,1) #Interested Party # - Submitting publisher's unique identifier for this publisher.
		process(28,6,'',1) #Constant - set this field equal to spaces.
		process(34,5,getRecordDetail(recordNumber, 'OOperfOwned'),0,1) #PR COllection Share - Defines the percentage of the total royalty distributed for performance of the work which will be collected by (paid to ) the publisher within the above territory. it can be range from 0 to 50.00.
		process(39,5,getRecordDetail(recordNumber, 'OOmechOwned'),0,1) #MR COllection share - percentage of the total royalty distributed for sales of CD, Cassette tapes. in which the work is included which will be collected by (paid to) the publisher. I can range from 0 to 100.00.
		process(44,5) #Defines the percentage of the total royalty distributed for synch rights to the work which will be collected by the publisher. range from 0 to 100.00.
		#Version 2.0 field
		process(49,1, 'I') #Inclusion/Exclusion Indicator - marker which shows whether the territory specified in the record is part of the territorial scope of the agreement or not possible entries are I (=territory included) and E (=territory excluded)
		process(50,4,'2109') #TIS Numeric Code - territory within which this publisher claims the right to collect payment for performance or use of this work. these alues reside in the TIS Code Table. 
		process(54,1,'N',1) #share change - if the share for the writer interest change as a result of sub publication in this terrtory or for a similar reason, set this field to "Y".
		#version 2.1 Fields
		process(55,3,'001') #sequence number - assigned to each territory following an SPU. 
		self.addCount()
		self.endRecord()

	def SPT1(self, recordNumber, publisher=0): #This is a duplicate of interested party, but calculates the sum of all controlled writers
		sumMech =sum([floatSafe(getRecordDetail(p, 'mechOwned')) for p in range(position, self.end) if getRecordDetail(p, 'controlled') == 'Y'])
		sumPerf =sum([floatSafe(getRecordDetail(p, 'perfOwned')) for p in range(position, self.end) if getRecordDetail(p, 'controlled') == 'Y'])
		process(0,3,'SPT') #record prefix - set record type = SPT (Publisher Territory of Control)
		process(3,8, str(self.songNumber),0,1)
		process(11,8, str(self.sequenceCount),0,1)
		process(19,9,getRecordDetail(recordNumber, 'clientID'),0,1) #Interested Party # - Submitting publisher's unique identifier for this publisher.
		process(28,6,'',1) #Constant - set this field equal to spaces.
		process(34,5,getRecordDetail(recordNumber, 'OOperfOwned'),0,1) #PR COllection Share - Defines the percentage of the total royalty distributed for performance of the work which will be collected by (paid to ) the publisher within the above territory. it can be range from 0 to 50.00.
		process(39,5,getRecordDetail(recordNumber, 'OOmechOwned'),0,1) #MR COllection share - percentage of the total royalty distributed for sales of CD, Cassette tapes. in which the work is included which will be collected by (paid to) the publisher. I can range from 0 to 100.00.
		process(44,5) #Defines the percentage of the total royalty distributed for synch rights to the work which will be collected by the publisher. range from 0 to 100.00.
		#Version 2.0 field
		process(49,1, 'I') #Inclusion/Exclusion Indicator - marker which shows whether the territory specified in the record is part of the territorial scope of the agreement or not possible entries are I (=territory included) and E (=territory excluded)
		process(50,4,'2127') #TIS Numeric Code - territory within which this publisher claims the right to collect payment for performance or use of this work. these alues reside in the TIS Code Table. 
		process(54,1,'N',1) #share change - if the share for the writer interest change as a result of sub publication in this terrtory or for a similar reason, set this field to "Y".
		#version 2.1 Fields
		process(55,3,'002') #sequence number - assigned to each territory following an SPU. 
		self.addCount()
		self.endRecord()

	def SPT2(self, recordNumber, publisher=0): #This is a duplicate of interested party, but calculates the sum of all controlled writers
		sumMech =sum([floatSafe(getRecordDetail(p, 'mechOwned')) for p in range(position, self.end) if getRecordDetail(p, 'controlled') == 'Y'])
		sumPerf =sum([floatSafe(getRecordDetail(p, 'perfOwned')) for p in range(position, self.end) if getRecordDetail(p, 'controlled') == 'Y'])
		process(0,3,'SPT') #record prefix - set record type = SPT (Publisher Territory of Control)
		process(3,8, str(self.songNumber),0,1)
		process(11,8, str(self.sequenceCount),0,1)
		process(19,9,getRecordDetail(recordNumber, 'clientID'),0,1) #Interested Party # - Submitting publisher's unique identifier for this publisher.
		process(28,6,'',1) #Constant - set this field equal to spaces.
		process(34,5,getRecordDetail(recordNumber, 'OOperfOwned'),0,1) #PR COllection Share - Defines the percentage of the total royalty distributed for performance of the work which will be collected by (paid to ) the publisher within the above territory. it can be range from 0 to 50.00.
		process(39,5,getRecordDetail(recordNumber, 'OOmechOwned'),0,1) #MR COllection share - percentage of the total royalty distributed for sales of CD, Cassette tapes. in which the work is included which will be collected by (paid to) the publisher. I can range from 0 to 100.00.
		process(44,5) #Defines the percentage of the total royalty distributed for synch rights to the work which will be collected by the publisher. range from 0 to 100.00.
		#Version 2.0 field
		process(49,1, 'I') #Inclusion/Exclusion Indicator - marker which shows whether the territory specified in the record is part of the territorial scope of the agreement or not possible entries are I (=territory included) and E (=territory excluded)
		process(50,4,'2111') #TIS Numeric Code - territory within which this publisher claims the right to collect payment for performance or use of this work. these alues reside in the TIS Code Table. 
		process(54,1,'N',1) #share change - if the share for the writer interest change as a result of sub publication in this terrtory or for a similar reason, set this field to "Y".
		#version 2.1 Fields
		process(55,3,'001') #sequence number - assigned to each territory following an SPU. 
		self.addCount()
		self.endRecord()

	def SPT3(self, recordNumber, publisher=0): #This is a duplicate of interested party, but calculates the sum of all controlled writers
		sumMech =sum([floatSafe(getRecordDetail(p, 'mechOwned')) for p in range(position, self.end) if getRecordDetail(p, 'controlled') == 'Y'])
		sumPerf =sum([floatSafe(getRecordDetail(p, 'perfOwned')) for p in range(position, self.end) if getRecordDetail(p, 'controlled') == 'Y'])
		process(0,3,'SPT') #record prefix - set record type = SPT (Publisher Territory of Control)
		process(3,8, str(self.songNumber),0,1)
		process(11,8, str(self.sequenceCount),0,1)
		process(19,9,getRecordDetail(recordNumber, 'clientID'),0,1) #Interested Party # - Submitting publisher's unique identifier for this publisher.
		process(28,6,'',1) #Constant - set this field equal to spaces.
		process(34,5,getRecordDetail(recordNumber, 'OOperfOwned'),0,1) #PR COllection Share - Defines the percentage of the total royalty distributed for performance of the work which will be collected by (paid to ) the publisher within the above territory. it can be range from 0 to 50.00.
		process(39,5,getRecordDetail(recordNumber, 'OOmechOwned'),0,1) #MR COllection share - percentage of the total royalty distributed for sales of CD, Cassette tapes. in which the work is included which will be collected by (paid to) the publisher. I can range from 0 to 100.00.
		process(44,5) #Defines the percentage of the total royalty distributed for synch rights to the work which will be collected by the publisher. range from 0 to 100.00.
		#Version 2.0 field
		process(49,1, 'I') #Inclusion/Exclusion Indicator - marker which shows whether the territory specified in the record is part of the territorial scope of the agreement or not possible entries are I (=territory included) and E (=territory excluded)
		process(50,4,'0528') #TIS Numeric Code - territory within which this publisher claims the right to collect payment for performance or use of this work. these alues reside in the TIS Code Table. 
		process(54,1,'N',1) #share change - if the share for the writer interest change as a result of sub publication in this terrtory or for a similar reason, set this field to "Y".
		#version 2.1 Fields
		process(55,3,'001') #sequence number - assigned to each territory following an SPU. 
		self.addCount()
		self.endRecord()

	def SWT(self, recordNumber, count):
		process(0,3,'SWT') #record prefix - set record type = SWT (writer territory of control)
		process(3,8, str(self.songNumber),0,1)
		process(11,8, str(self.sequenceCount),0,1)
		process(19,2,'')
		process(21,7,getRecordDetail(recordNumber, 'clientID'),0,1) #Interested Party number - submitting publisher unique identifier for this writer.
		process(28,5,getRecordDetail(recordNumber, 'OOperfOwned'),0,1) #PR Collection Share - Define the percentage of the total royalty distributed for performance of the work which will be collected by the writer within the above territory. within an SWT record, can be a range from 0 to 100.00.
		process(33,5) #MR Collection Shares - Define the percentage of the total royalty fistributed for sales of CD, Cassette tapes, etc. in which the work is included which will be collected by the writer. within an SWT record, can be range from 0 to 100.00.
		process(38,5) #SR Collection share - defines the percentage of the total royalty distributed for synchronization rights to the work which will be collected by  the writer. within an SWT record, can be a range from 0 to 100.00.
		process(43,1, 'I') #Includsion/Exclusion Indicator - This is a marker which shows whether the territory specified in theis record is part of the territorial scope of the agreement or not possible entries are I (= territory included) and E (= territory excluded).
		process(44,4,'2136') #(America 2101) TIS Numeric code - a territory within which this writer has the right to collect payment for performance of this work. these value reside in the TIS code table.
		process(48,1,'N') #Shares Change - IF the shares for the writer interest change as a result of sub-publication in this territory set this field to "Y".
		#version 2.1f fields
		process(49,3,'001') #sequence number - a sequntial numbe assigned to each territory following an SWR.
		self.addCount()
		self.endRecord()

	def OWR(self, recordNumber):
		process(0,3,'OWR') #Record Prefix - Set Record Type = OWR (Other WRiter)
		process(3,8, str(self.songNumber),0,1)
		process(11,8, str(self.sequenceCount),0,1) 
		process(19,2,'')
		process(21,7) #interested party number - submitting publisher's unique identifier for this writer. this field is require for record type SWR and optional for record type OWR. 
		process(28,45,getRecordDetail(recordNumber, 'OlastName'),1) #Writer last name - The Last Name of this writer. *if the submitter does not have the ability to split first and last names, the entire name should be entered in this field in the format "last name, first name" including the comma after the last name. This field is required for record type SWR and optional for record type OWR.
		process(73,30,getRecordDetail(recordNumber, 'OfirstName'),1) #Writer First Name - the first name of this wrtier along with all qualifying and middle names.
		process(103,1,'',1) #Writer Unknown indicator - indicates if tehe name of this writer is unknown. note that this field must be left blank for SWR records. For OWR records, this field must be set to "Y" if the writer last name is blank.
		process(104,2, 'CA',1) #Writer Designation Code - Code defining the role the writer played in the composition of the work. these values reside in the writer designation table. this field is require for record type SWR and optional for record type OWR.
		process(106,9,'',1) #Tax ID number - number used to identify this writer for domestic tax reporting.
		process(115,2)
		process(117,9,getRecordDetail(recordNumber, 'writerCAE'),0,1) #writer IPI Name number - The IPI name # assigned to this writer. 
		process(126,3, getRecordDetail(recordNumber, 'writerSocietyCWR')[:3],1) #PR Affiliation society number - mumber assigned to the performing rights society with which the writer is affiliated. these values reside on the society code table. 
		process(129,5,getRecordDetail(recordNumber, 'OOperfOwned'),0,1) #PR ownership share - defines the percentage of the writer's ownership of the performance rights to the work. within an individual SWR record, this value can range from 0 to 100.0. *writer both own and collect the performing right interest. 
		process(134,3, getRecordDetail(recordNumber, 'writerSocietyCWR')[:3],1) #MR society - number assigned to the mechanical rights society with which the writer is affiliated. these alues reside on the society code table. 
		process(137,5) #MR ownership share - the percentage of the writer's ownership of the mechanical rights to the work. within an individual SPU record, this value can range from 0 to 100.00.
		process(142,3,'',1) #SR society - number assigned to the mechanical rights society with which the publisher is affiliated. these values reside on the society code table.
		process(145,5) #SR Ownership Share - define the percentage of the writer's ownership of the synch rights to the work. within an individual SPU record, this value can range from 0 to 100.0.
		#Society/region specific fields
		process(150,1,'',1) #reversionary indicator - indicates writer involved in the claim
		process(151,1,'',1) #indicates whether the submitter has refused to give authority for the first recording *field is mandatory for registration with the UK societies.
		process(152,1,'',1) #Work for hire indicator filler - indicates whether or not this work was written for hire.
		#version 2.0 fields
		process(154,13,'',1) #writer IPI base number - the IPI based assigned to this writer. these values reside in the IPI database. 
		process(167,12,'',1) #personal number - personal number assigned to this writer in the country of his/her residence.
		#version 2.1 fields
		process(179,1,'',1) #indicates that rights flow through SESAC/BMI ?ASCAP in the US.
		self.addCount()
		self.endRecord()
		
	def SWR(self, recordNumber, publisher=0):   #This defaults to composer.  If record is a publisher record, publisher = 1
		process(0,3,'SWR') #Record Prefix - Set Record Type = SWR (Writer controlled by submitter)
		process(3,8, str(self.songNumber),0,1)
		process(11,8, str(self.sequenceCount),0,1)
		process(19,2,'',0,1)
		process(21,7,getRecordDetail(recordNumber, 'clientID'),0,1) #interested party number - submitting publisher's unique identifier for this writer. this field is require for record type SWR and optional for record type OWR. 
		process(28,45,getRecordDetail(recordNumber, 'lastName'),1) #Writer last name - The Last Name of this writer. *if the submitter does not have the ability to split first and last names, the entire name should be entered in this field in the format "last name, first name" including the comma after the last name. This field is required for record type SWR and optional for record type OWR.
		process(73,30,getRecordDetail(recordNumber, 'firstName'),1) #Writer First Name - the first name of this wrtier along with all qualifying and middle names.
		process(103,1,'',1) #Writer Unknown indicator - indicates if tehe name of this writer is unknown. note that this field must be left blank for SWR records. For OWR records, this field must be set to "Y" if the writer last name is blank.
		process(104,2,'CA',1) #Writer Designation Code - Code defining the role the writer played in the composition of the work. these values reside in the writer designation table. this field is require for record type SWR and optional for record type OWR.
		process(106,9,'',1) #Tax ID number - number used to identify this writer for domestic tax reporting.
		process(115,2)
		process(117,9,getRecordDetail(recordNumber, 'writerCAE'),0,1) #writer IPI Name number - The IPI name # assigned to this writer. 
		process(126,3, getRecordDetail(recordNumber, 'writerSocietyCWR')[:3],1) #PR Affiliation society number - mumber assigned to the performing rights society with which the writer is affiliated. these values reside on the society code table. 
		process(129,5,getRecordDetail(recordNumber, 'OOperfOwned'),1) #PR ownership share - defines the percentage of the writer's ownership of the performance rights to the work. within an individual SWR record, this value can range from 0 to 100.0. *writer both own and collect the performing right interest. 
		process(134,3, getRecordDetail(recordNumber, 'writerSocietyCWR')[:3],1) #MR society - number assigned to the mechanical rights society with which the writer is affiliated. these alues reside on the society code table. 
		process(137,5) #MR ownership share - the percentage of the writer's ownership of the mechanical rights to the work. within an individual SPU record, this value can range from 0 to 100.00.
		process(142,3,'',1) #SR society - number assigned to the mechanical rights society with which the publisher is affiliated. these values reside on the society code table.
		process(145,5) #SR Ownership Share - define the percentage of the writer's ownership of the synch rights to the work. within an individual SPU record, this value can range from 0 to 100.0.
		process(150,1,'',1) #reversionary indicator - indicates writer involved in the claim.
		process(151,1,'',1) #indicates whether the submitter has refused to give authority for the first recording *field is mandatory for registration with the UK societies.
		process(152,1,'',1) #Work for hire indicator filler - indicates whether or not this work was written for hire.
		#version 2.0 fields
		process(154,13,'',1) #writer IPI base number - the IPI based assigned to this writer. these values reside in the IPI database.
		process(167,12,'',1) #personal number - personal number assigned to this writer in the country of his/her residence.
		#version 2.1 fields
		process(179,1,'',1) #indicates that rights flow through SESAC/BM ?ASCAP in the US.
		self.addCount()
		self.endRecord()

	def OPU(self, recordNumber):
		process(0,3,'OPU') #Record Prefix - Set record type =  OPU (Other publisher)
		process(3,8, str(self.songNumber),0,1)
		process(11,8, str(self.sequenceCount),0,1)
		process(19,2, str(self.sequenceCount2),0,1) #publisher sequence number - sequential number assigned to the origional publisher on this work. 
		process(21,9, getRecordDetail(recordNumber, 'publisherID'),0,1) #interested party - publisher's unique identifier for this publisher. this field is require for record type SPU and optional for record type OPU.
		process(30,45, getRecordDetail(recordNumber, 'publisherName') or "UNKNOWN PUBLISHER",1) #publisher name - name of publishing company. field is require for record type SPU and optional for record type OPU. 
		process(75,1,'',1) #Publisher unknown indicator - Indicates if the name of the publisher is unkonwn. *that this field must be left blank for SPU records. For OPU records. this field must be set to "Y" if the publisher name is blank. 
		process(76,2,'E',1) #Publisher Type - Code defining this publisher's role in the publishing of the work. these values reside on the publisher type table. this field is required for record type SPU and optional for record type OPU. 
		process(78,9,'',1) #tax ID number - the number used to identify this publisher for domestic tax reporting.
		process(87,2)
		process(89,9,getRecordDetail(recordNumber, 'publisherCAE') or "000000000",0,1)	 #publisher IPI Name number - IPI name assign to this publisher. 
		process(98,14,'',1) #Submitter Agreement number - Indicates the agreement number unique to the submitter under which this publisher has acquired the rights t this work.
		process(112,3, getRecordDetail(recordNumber, 'publisherSocietyCWR')[:3],1) #PR Affiliation Society # - Number assigned to the performing rights society with which the publisher is affiliated. these values reside on the society code table.
		process(115,5,getRecordDetail(recordNumber, 'OOperfOwned'),0,1) #PR Ownership share - defines the percentage of the publisher's ownership of the performacen riths to the work. share does not define the percentage of the total royalty distributed for performance of the work that will be collected by the publisher within an individual SPU record this value can range from 0 to 50.0.
		process(120,3, getRecordDetail(recordNumber, 'publisherSocietyCWR')[:3],1) #MR society - number assigned to the mechanical rights society with which the publisher is affiliated. these values reside on the society code table.
		process(123,5,getRecordDetail(recordNumber, 'OOmechOwned'),0,1) #MR Ownership Share - percentage of the publisher ownership of the mechanical rights to the work. this share does not define the percentage of the total royalty distrbuted for sale of CD CAssettes. containing the work that will be collected by the publisher within an individual SPU record this  value can range from 0 to 100,000
		process(128,3, getRecordDetail(recordNumber, 'publisherSocietyCWR')[:3],1) #SR Society - Number assigned to the society with which the publisher is affiliated for administration of synchronization rights. these values reside on the society code table. 													
		process(129,5,getRecordDetail(recordNumber, 'OOmechOwned'),0,1) #SR Ownership Share - defines the percentage of the publisher's ownership of the synch rights to the work. this share does not define the percentage of the total money distributed that will be collected by the publisher. within an indivudual SPU record. this value can range from 0 to 100.00.
		process(134,1,'',1) #spcial agreement indicator - indicates publisher claiming reversionary rights. *note that this flag only applies to societies that recognize reversionary rights (for example, SOCAN)
		process(135,1,'N') #fire recording refusal ind - indicates whether the submitter has refused to give authority for the first recording. *this field is mandatory for registraton with the UK societies.
		process(136,1,'',1) #filler - fill with a blank
		#version 2.0 fields
		process(137,13,'',1) #publisher IPI Base Number - IPI Base number assigned to this publisher.
		process(150,14,'',1) #International stadard agreement code - ISAC that has been assigned t the agreement under which the publisher share is to be adminitered.
		process(164,14,'',1) #society-assigned agreement number - the agreement number assigned by the society of the subpublisher.
		#version 2.1 fields
		process(178,2,'',1) #agreement type - code defining the category of agreement. the values reside in the agreement type table.
		process(180,1,'',1) #USA License Ind - indicates that rights flow through SESAC/BMI/ASCA in the US.
		self.addCount()
		self.endRecord()

	def PWR(self, recordNumber):
		process(0,3,'PWR') #Record Prefix - Set record type = PWR (Publisher for writer)
		process(3,8, str(self.songNumber),0,1)
		process(11,8, str(self.sequenceCount),0,1) 
		process(19,9,getRecordDetail(recordNumber, 'clientID'),0,1)#publisher IPI number - The Publisher interested part number pointing back to the SPU record for the origingal publisher/income participant representing this writer. 
		process(28,45, getRecordDetail(recordNumber, 'publisherName') or "UNKNOWN PUBLISHER",1) #Publisher Name - the name of the publisher indicated by the interested party number field.
		#version 2.0 field
		process(73,14,getRecordDetail(recordNumber, 'publisherCAE') or "000000000",0,1) #submitter agreement number - the unique number assigned to this agreement by the submitter.
		process(87,14,getRecordDetail(recordNumber, 'PRSAgreementNumber'),0,1) #society-assigned agreement number - the unique number assigned to this agreement by the society.
 		#version 2.1 field
 		process(102,9,getRecordDetail(recordNumber, 'writerCAE'),0,1)  #writer IPI number - the witer interested party number pointing back to the SWR record in an explicit link.
		self.addCount()
		self.endRecord()

	def ALT(self, recordNumber):	#extra def that i created.
		process(0,3,'ALT') #record prefix - set record type - ALT (Alternate title)
		process(3,8, str(self.songNumber),0,1)
		process(11,8, str(self.sequenceCount),0,1)
		process(19,60,getRecordDetail(recordNumber, 'AlternateTitles'),1) #AKA or pseudonym of the work title.
		process(79,2,'AT') #Indicates the type of the alternate title presented on this record.
		process(81,2,'EN') #language code - the code representing the language that this alternate title has been translated into. these values reside in the language code table. a language code must be entered if the Title Type is equal to "OL" or "AL".
		self.addCount()
		self.endRecord()

	def PER(self, recordNumber):	#extra def for "artist" that i created.
		process(0,3,'PER')
		process(3,8, str(self.songNumber),0,1)
		process(11,8, str(self.sequenceCount),0,1)
		process(19,45,getRecordDetail(recordNumber, 'artist'),1)
		self.addCount()
		self.endRecord()

def fileHeaderRecord(): #transmission header
	process(0,3,'HDR') #record type
	process(3,2,'PB') #sender type
	process(3,9, '257809238') #our CWR sender ID code is "MLM"
	process(14,45,'MISSING LINK MUSIC LLC',1) #sender name (what is ,1 mean?)
	process(61,5, '01.10') #EDI Standard Version Number - Indicates which version of the header and trailer records was used to create the data in the file. This field must be set to 01.10 for this version of the standard.
	process(66,8,getDate(0, 'currentDate'),1) #Creation Date ??
	process(74,6) #creation time ??
	process(80,8,getDate(0, 'currentDate'),1) #transmission date ??
	process(88,15,'',1) #character Set ??
	output.write("\n")

def groupHeaderRecord(): #group header
	process(0,3,'GRH') #GRH = group header
	process(3,3,'NWR') #type of transactions. could be NWR = new work registration or REV = revised registration
	process(6,5,'00001') #group ID ??
	process(11,5, '02.10') #version number for this transaction - for CWR version 2.1, set to 02.10
	process(16,10) #Batch request a unique sequential number to identify the group. this numer is managed by the submitter to identify the group amoung multiple submission files.
	output.write("\n")

#"""The 2nd to last line of CWR which is 'Group Trailer' that starts with 'GRT' """ indicates the end of a group and provides both transaction and record counts for the group.
def groupTrailerRecord():
	process(0,3,'GRT')
	process(3,5,'00001') #Group ID - the same group id that was present on the preceding GRH record. *must be equal to the group ID presented on the previous GRH record. (GR)
	process(8,8,'%s' % songCount,0,1) #Transaction Count - the number of transactions included within this group. *Must be equal to the total number of transactions within this group (GR)
	process(16,8,'%s' % recordCount ,0,1) #Record Count - the number of physical records included within this group. ??? *must be equal to the total number of physical records inclusive of the GRH and GRT records. (GR)
	output.write("\n")

def fileTrailerRecord():
	process(0,3,'TRL')
	process(3,5,'00001') #Group Count - The number of groups included within this file. *group cout must be equal to the number of groups within the entire file. (ER)
	process(8,8,'%s' % songCount,0,1) #Transacation Count - The number of transacations included within this file. * must be equal to the number of transacations within the entire file.
	process(16,8,('%s' % recordCount),0,1) #the number of physical records included in this file including HDR and TRL records. ??? *must be equal to the number of physical record inclusive of the HDR and TRL records.


def process(num1, num2, data='0', space=0, just=0):
	def writeSpace():
		(just and [output.write(data.rjust(num2, ' '))] or [output.write(data.ljust(num2, ' '))])[0]
	def writeZero():
		(just and [output.write(data.rjust(num2, '0'))] or [output.write(data.ljust(num2, '0'))])[0]
	(space and [writeSpace()] or [writeZero()])[0]

def getRecordDetail(recordNumber=0, field = ''):
	currentRecord = allRecords[recordNumber]
	fieldValue = currentRecord['%s' % (field)]
	return fieldValue
	
def floatSafe(value):
	try:
		goodValue = float(value)
		return goodValue
	except:
		return 0
		
def processPub(recordNumber):
	if getRecordDetail(recordNumber, 'controlled') == 'Y':
		[process(34.5,convertPercent(recordNumber,'perfOwned'),0,1),
		 process(39.5,convertPercent(recordNumber,'mechOwned'),0,1),
		 process(80,38)]
	else:
		[process(66,7,convertPercent(recordNumber,'mechOwned'),0,1),
		 process(73,7,convertPercent(recordNumber,'perfOwned'),0,1),
		 process(80,12),
		 process(92,7,convertPercent(recordNumber,'mechOwned'),0,1),
		 process(99,7,convertPercent(recordNumber,'perfOwned'),0,1),
		 process(106,12)]


def divideSongs():
	global songCount
	global songRecordLength
	idList = ["%s" % (allRecords[p]['songID']) for p in range(len(allRecords))]
	dividedIdList = [list(g) for k, g in groupby(idList)]
	songCount = len(dividedIdList)
	songRecordLength = [len(dividedIdList[p]) for p in range(len(dividedIdList))]
	

def execute():
	fileHeaderRecord()
	groupHeaderRecord()
	t = [Song(p) for p in range(songCount)]
	groupTrailerRecord()
	fileTrailerRecord()

divideSongs()
execute()
