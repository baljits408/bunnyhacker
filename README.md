# pdirt Makefile
include ./Makefile.config
 
WORLD =	$(WORLDDIR)/ancient.area	$(WORLDDIR)/abyss.area \
	$(WORLDDIR)/blizzard.area	$(WORLDDIR)/camelot.area \
	$(WORLDDIR)/castle.area \
	$(WORLDDIR)/catacomb.area	$(WORLDDIR)/cathedral.area \
	$(WORLDDIR)/cave.area		$(WORLDDIR)/church.area \
	$(WORLDDIR)/dead.area \
	$(WORLDDIR)/doom.area \
	$(WORLDDIR)/eastcoast.area	$(WORLDDIR)/eforest.area \
	$(WORLDDIR)/fantasy.area	$(WORLDDIR)/femnaz.area \
	$(WORLDDIR)/forest.area		$(WORLDDIR)/frobozz.area \
	$(WORLDDIR)/haven.area \
	$(WORLDDIR)/heaven.area		$(WORLDDIR)/home.area \
	$(WORLDDIR)/icecave.area	$(WORLDDIR)/intro.area \
	$(WORLDDIR)/island.area		$(WORLDDIR)/jail.area \
	$(WORLDDIR)/jbs.area		$(WORLDDIR)/kastle.area \
	$(WORLDDIR)/labyrinth.area	$(WORLDDIR)/ledge.area \
	$(WORLDDIR)/limbo.area		 \
	$(WORLDDIR)/lww.area		$(WORLDDIR)/mcastle.area \
	$(WORLDDIR)/mithdan.area \
	$(WORLDDIR)/moor.area		$(WORLDDIR)/mountain.area \
	$(WORLDDIR)/mountain.area	$(WORLDDIR)/newbie.area \
	$(WORLDDIR)/newbie.area		$(WORLDDIR)/newvillage.area \
	$(WORLDDIR)/nibelung.area	$(WORLDDIR)/oaktree.area \
	$(WORLDDIR)/orchold.area	$(WORLDDIR)/party.area \
	$(WORLDDIR)/pirate.area \
	$(WORLDDIR)/pit.area		$(WORLDDIR)/plain.area \
	$(WORLDDIR)/powers.area		$(WORLDDIR)/quarry.area \
	$(WORLDDIR)/sea.area		$(WORLDDIR)/sherwood.area \
	$(WORLDDIR)/start.area		$(WORLDDIR)/stjames.area \
	$(WORLDDIR)/tower.area		$(WORLDDIR)/townhall.area \
	$(WORLDDIR)/treehouse.area	\
	$(WORLDDIR)/valley.area		$(WORLDDIR)/village.area \
	$(WORLDDIR)/waste.area \
	$(WORLDDIR)/window.area		\
	$(WORLDDIR)/special.h \
	$(WORLDDIR)/files  		$(WORLDDIR)/undef.h
 
DRVROBJ =actions.o admin.o atsys.o board.o bootstrap.o bprintf.o calendar.o \
	change.o clone.o client.o commands.o communicate.o debug.o dns.o \
	editor.o exit.o fight.o flags.o hate.o infosys.o log.o magic.o mail.o \
	main.o misc.o mobile.o move.o mud.o objsys.o parse.o party.o quest.o \
	recover.o rooms.o s_socket.o sendsys.o sflag.o special.o \
	syslog.o system.o timing.o utils.o ver.o wizard.o \
	wizlist.o writer.o zones.o uaf.o corpse.o 
 
all: generator world filter driver 
	@$(ECHO) 'Mud is up to date.'
 
buildversion:
	@if [ -f .LINKCOUNT ]; then \
	    expr `cat .LINKCOUNT` + 1 > .LINKCOUNT; \
	else \
	    $(ECHO) '1' > .LINKCOUNT; \
	fi
	@$(ECHO) '#ifndef PDIRT_BUILD_H_' > $(INCLUDEDIR)/build.h
	@$(ECHO) '#define PDIRT_BUILD_H_' >> $(INCLUDEDIR)/build.h
	@$(ECHO) "#define LINKCOUNT\t	`cat .LINKCOUNT`" >> $(INCLUDEDIR)/build.h
	@$(ECHO) '#endif' >> $(INCLUDEDIR)/build.h
 
utils:
	@cd $(UTILDIR); $(MAKE) utils -f $(MAKEFILE)
 
generator:
	@cd $(COMPILERDIR); $(MAKE) mudcompile
 
depend:
	@cd $(UTILDIR); $(MAKE) depend -f $(MAKEFILE) 
	@$(DEPEND)
	@cd $(IMUDDIR); $(MAKE) depend -f $(MAKEFILE)
 
driver: buildversion $(DRVROBJ)  
	@$(ECHO) 'Moving old driver...'
	@-$(MV)	$(DRIVER) $(DRIVER).old
	@$(ECHO) 'Assembling Driver..'
	@$(CC) -o $(DRIVER) $(CFLAGS) $(DRVROBJ) $(DLIBS)
	@$(ECHO) 'Driver is up to date.'
	@-rm aberd
	@ln -s ../bin/aberd aberd
 
filter: misc.o pfilter.o
	@$(CC) -o $(BINDIR)pfilter $(CFLAGS) misc.o pfilter.o
 
InterMudLib:
	@cd $(IMUDDIR);$(MAKE) -f $(MAKEFILE)
	
	 
world:	$(DATADIR)locations $(DATADIR)objects $(DATADIR)mobiles \
	$(DATADIR)zones $(INCLUDEDIR)mobiles.h $(INCLUDEDIR)objects.h \
	$(INCLUDEDIR)locations.h $(INCLUDEDIR)zonetab.h
 
minimum:
	@$(RM) *.o
	
$(DATADIR)locations $(DATADIR)objects $(DATADIR)mobiles \
$(DATADIR)zones $(INCLUDEDIR)mobiles.h $(INCLUDEDIR)objects.h \
$(INCLUDEDIR)locations.h $(INCLUDEDIR)zonetab.h: $(WORLD) $(BINDIR)/mudcompile
	@cd ../areas;genworld	
 
verbs.h: $(DATADIR)verbs.src
	@$(BINDIR)/generate -v 
 
 
clean:
	@$(BINDIR)/cleanex
 
# DO NOT DELETE THIS LINE -- mkdep uses it.
# DO NOT PUT ANYTHING AFTER THIS LINE, IT WILL GO AWAY.
