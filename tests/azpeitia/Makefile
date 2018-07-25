####################################################################
## Project name: 
## 
##
####################################################################


## Software configuration.
SPLAT=splat-std -olditm
# Use metric system.
SPLAT += -metric
# Use city database.
SPLAT += -s herriak.dat
# Add analysis radius. (ie 60km)
SPLAT += -R 60

## Points.
POINT_AZPEITIA_XOXOTE=azpeitia-xoxote.qth

## Default target.
help:
	@echo "all : Build all targets."
	@echo "clean : Clean all generated files."
	@echo "SPLAT=$(SPLAT)"
	@echo ""

#########################################################
## Coverage analysis.
## $(eval $(call coverage,NAME,RXACL,-t TXPOINT[...]))
## NAME    : Analisis name.
## TXPOINT : qth filename, Transmiter location.
## RXACL   : Receptor altitide above ground level.
define coverage

.PHONY: coverage-$(1) help-coverage-$(1)
all: coverage-$(1)
coverage-$(1): out/coverage-$(1).ppm
out/coverage-$(1).ppm:
	@install -d out
	$(SPLAT) -c $(2) $(3) -o out/coverage-$(1).ppm
help: help-coverage-$(1)
help-coverage-$(1):
	@echo "coverage-$(1): Visual coverage analysis at $(2) AGL high."
	@echo "  Arguments: $(3)"
	@echo "  Output file: out/coverage-$(1).ppm"

endef


##########################################################
## Path loss analysis.
## $(eval $(call pathloss,NAME,RXACL,-t TXPOINT[...] [-db MAXDB],FREQ))
## NAME    : Analisis name.
## TXPOINT : qth filename, Transmiter location.
## RXACL   : Receptor altitide above ground level.
## MAXDB   : Maximun losses to show coloured.
## FREQ    : Transmited frequency in MHz.
define pathloss

.PHONY: pathloss-$(1) help-pathloss-$(1)
all: pathloss-$(1)
pathloss-$(1): out/pathloss-$(1).ppm
out/pathloss-$(1).ppm:
	@install -d out
	$(SPLAT) -L $(2) $(3) -f $(4) -o out/pathloss-$(1).ppm
help: help-pathloss-$(1)
help-pathloss-$(1):
	@echo "pathloss-$(1): Calculate path loss at $(2) AGL high receptor"
	@echo "  Arguments: $(3)"
	@echo "  Output file: out/coverage-$(1).ppm"
	@echo "  Frequency: $(4)"

endef
###########################################################
## Clean target.
clean:
	rm -rf out

$(eval $(call coverage,azpeitia,30,-t $(POINT_AZPEITIA_XOXOTE)))
$(eval $(call pathloss,azpeitia,30,-t $(POINT_AZPEITIA_XOXOTE),40))
