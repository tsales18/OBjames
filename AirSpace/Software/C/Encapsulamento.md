Encapsulamento é o conceito de **esconder detalhes de implementação** e expor apenas uma interface controlada. Em C, conseguimos isso usando:

[^1]
[^1]: // .h
	df1_packet_t* get_pacote_envio(void);
	void set_pacote_envio(df1_packet_t *novo);
	
	// .c
	static df1_packet_t pacote_privado;  // static = só neste arquivo
	
	df1_packet_t* get_pacote_envio(void) {
	    return &pacote_privado;
	}
	
	void set_pacote_envio(df1_packet_t *novo) {
	    if(novo) {
	        memcpy(&pacote_privado, novo, sizeof(df1_packet_t));
	    }
	}
	
	// Uso:
	df1_packet_t *p = get_pacote_envio();
	p->bytes.cmd = 0x01;
