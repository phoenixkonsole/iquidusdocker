papara=paparad
paparaCLI=papara-cli
B1_FLAGS=
B2_FLAGS=
B1=-datadir=1 $(B1_FLAGS)
B2=-datadir=2 $(B2_FLAGS)
BLOCKS=1
ADDRESS=
AMOUNT=
ACCOUNT=

start:
	$(papara) $(B1) -daemon -txindex
	$(papara) $(B2) -daemon -txindex

generate:
	$(paparaCLI) $(B1) setgenerate true $(BLOCKS)

getinfo:
	$(paparaCLI) $(B1) getinfo
	$(paparaCLI) $(B2) getinfo

sendfrom1:
	$(paparaCLI) $(B1) sendtoaddress $(ADDRESS) $(AMOUNT)

sendfrom2:
	$(paparaCLI) $(B2) sendtoaddress $(ADDRESS) $(AMOUNT)

address1:
	$(paparaCLI) $(B1) getnewaddress $(ACCOUNT)

address2:
	$(paparaCLI) $(B2) getnewaddress $(ACCOUNT)

stop:
	$(paparaCLI) $(B1) stop
	$(paparaCLI) $(B2) stop

clean:
	find 1/regtest/* -not -name 'server.*' -delete
	find 2/regtest/* -not -name 'server.*' -delete