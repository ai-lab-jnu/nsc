# 건강보험 PostgreSQL DDL


- 건강보험 활용 데이터의 PostgreSQL 생성 스크립트
    - [건강보험 빅데이터 활용 교육 실습 자료  기반](https://nhiss.nhis.or.kr/bbs/boards/notice/963.do)

> Note
>
> 아래 Table 생성 스크립트에서 TABLESPACE jnu_db; 는 각자의 PostgreSQL TABLESPACE 에 맞추어서 수정해야 한다



```
-- Table: nsc2_bnc_1000

-- DROP TABLE nsc2_bnc_1000;

CREATE TABLE nsc2_bnc_1000
(
    rn_indi integer,
    std_yyyy integer,
    sex integer,
    sgg integer,
    gaibja_type integer,
    ctrb_q10 integer,
    dsb_svrt_cd character(1) COLLATE pg_catalog."default" DEFAULT NULL::bpchar,
    dsb_type_cd character(1) COLLATE pg_catalog."default" DEFAULT NULL::bpchar,
    g1e_obj_yn character(1) COLLATE pg_catalog."default" DEFAULT NULL::bpchar,
    smpl_type_cd integer
)

TABLESPACE jnu_db;

ALTER TABLE public.nsc2_bnc_1000
    OWNER to postgres;
COMMENT ON TABLE nsc2_bnc_1000
    IS '자격 및 보험료 테이블

?   자격자료(사회경제적 변수) : 각 해당년도 연초 자격 기준
- 건강보험가입자 및 의료급여 수급권자로 구성 (외국인 제외)
?   직장 및 지역가입자 각 10분위, 의료급여수급권자 0분위(null 값)

※ 직장, 지역의 같은 분위가 같은 소득수준을 뜻하는 것은 아님
※ 의료급여 대상자의 보험료는 결측

시군구코드

?   구성(다섯 자리): 시도(두 자리) + 시군구(세 자리)
예) 시군구 코드 ‘11110’: ‘11’(서울시) + ‘110’(종로구)
- 시군구 코드는 해당시점 유효한 행정안전부 주민등록주소코드로 변환
※ 시군구 행정구역 개편에 의한 연도별 행정구역의 변경사항을 검토하여 활용
- 결측이거나 시도 단위까지만 존재하는 등 유효하지 않은 경우 가장 근접한 유효 시군
구 코드로 대체
- 연평균 대상자수가 500인 미만인 4개 군은 인접 군과 코드 통합
';

COMMENT ON COLUMN nsc2_bnc_1000.rn_indi
    IS '개인고유번호(7자리), 연계변수 (주민등록번호의 대체 식별번호)';

COMMENT ON COLUMN nsc2_bnc_1000.std_yyyy
    IS '기준년도 (2002~2015년 자격 및 의료이용 해당년도)';

COMMENT ON COLUMN nsc2_bnc_1000.sex
    IS '성별 (1: 남자, 2: 여자)';

COMMENT ON COLUMN nsc2_bnc_1000.sgg
    IS '시군구

시도 2자리와 시군구 3자리로 구성
해당년도의 유효 시군구 정보';

COMMENT ON COLUMN nsc2_bnc_1000.gaibja_type
    IS '가입자 구분

1: 지역세대주
2: 지역세대원
5: 직장가입자
6: 직장피부양자
7: 의료급여세대주
8: 의료급여세대원';

COMMENT ON COLUMN nsc2_bnc_1000.ctrb_q10
    IS '보험료분위 10분위 (직장 및 지역가입자 각 10분위, 의료급여수급권자 0분위)';

COMMENT ON COLUMN nsc2_bnc_1000.dsb_svrt_cd
    IS '장애중증도 구분

0: 해당없음
1: 중증 (1~2등급)
2: 경증 (3~6등급)';

COMMENT ON COLUMN nsc2_bnc_1000.dsb_type_cd
    IS '장애유형 구분

주요 장애 (지체, 뇌병변, 시각, 청각장애) 외의 장애는 기타장애로 그룹화';

COMMENT ON COLUMN nsc2_bnc_1000.g1e_obj_yn
    IS '건강검진 (일반 1차) 대상자 여부 (Y: 대상자)';




-- Table: nsc2_bnd_1000

-- DROP TABLE nsc2_bnd_1000;

CREATE TABLE nsc2_bnd_1000
(
    rn_indi integer,
    bth_yyyy character varying(10) COLLATE pg_catalog."default" DEFAULT NULL::character varying,
    dth_yyyymm character varying(10) COLLATE pg_catalog."default" DEFAULT NULL::character varying,
    cod1 character varying(10) COLLATE pg_catalog."default" DEFAULT NULL::character varying,
    cod2 character varying(10) COLLATE pg_catalog."default" DEFAULT NULL::character varying
)

TABLESPACE jnu_db;

ALTER TABLE public.nsc2_bnd_1000
    OWNER to postgres;
COMMENT ON TABLE nsc2_bnd_1000
    IS '출생 및 사망 테이블 개요

? 내용 : 표본의 출생연도, 사망연월 및 사망원인 정보

? 연령 :

-  대상자의 출생년도로 연령 산출 시 ‘기준년도’-‘출생년도’계산 필요

-  2006년 기준 85세 해당(1921년 및 그 이전 출생자)자 출생연도 ‘1921LE’
';

COMMENT ON COLUMN nsc2_bnd_1000.rn_indi
    IS '개인고유번호(7자리), 연계코드';

COMMENT ON COLUMN nsc2_bnd_1000.bth_yyyy
    IS '출생연도';

COMMENT ON COLUMN nsc2_bnd_1000.dth_yyyymm
    IS '사망자의 사망월 ... 통계청 사망원인 연계';

COMMENT ON COLUMN nsc2_bnd_1000.cod1
    IS '한국표준질병?사인분류(KCD) 코드 사용

- 범위 : A00-T98
-  일반적인 사망(소분류), 민감사인(중분류)
※ 일반적인 사망은(A00 ~ R99) 사망원인1만 있음
※ 사망원인';




-- Table: nsc2_d20_1000

-- DROP TABLE nsc2_d20_1000;

CREATE TABLE nsc2_d20_1000
(
    rn_indi integer,
    rn_key bigint,
    rn_inst integer,
    mdcare_strt_dt integer,
    form_cd character varying(10) COLLATE pg_catalog."default" DEFAULT NULL::character varying,
    mcare_subj_cd integer,
    sick_sym1 character varying(10) COLLATE pg_catalog."default" DEFAULT NULL::character varying,
    sick_sym2 character varying(10) COLLATE pg_catalog."default" DEFAULT NULL::character varying,
    fst_hsptz_dt character varying(10) COLLATE pg_catalog."default" DEFAULT NULL::character varying,
    hsptz_path_type character varying(10) COLLATE pg_catalog."default" DEFAULT NULL::character varying,
    ofij_type character varying(10) COLLATE pg_catalog."default" DEFAULT NULL::character varying,
    oprtn_yn integer,
    mdcare_dd_cnt integer,
    vshsp_dd_cnt integer,
    tot_prsc_dd_cnt integer,
    mcare_rslt_type integer,
    edc_add_rt double precision,
    ed_rc_tot_amt integer,
    edc_sba integer,
    edc_insur_brdn_amt integer,
    spcf_sym_type character varying(10) COLLATE pg_catalog."default" DEFAULT NULL::character varying,
    std_yyyy integer
)

TABLESPACE jnu_db;

ALTER TABLE public.nsc2_d20_1000
    OWNER to postgres;
COMMENT ON TABLE nsc2_d20_1000
    IS '치과 - 진료 명세서';

COMMENT ON COLUMN nsc2_d20_1000.rn_indi
    IS '개인고유번호(7자리), 연계코드';

COMMENT ON COLUMN nsc2_d20_1000.rn_key
    IS '청구고유번호 (14자리), 연계코드';

COMMENT ON COLUMN nsc2_d20_1000.rn_inst
    IS '요양기관고유번호(6자리), 연계코드';

COMMENT ON COLUMN nsc2_d20_1000.mdcare_strt_dt
    IS '요양 게시일자

- 수진자가 진료를 받기 시작한 일자
- 의과, 치과, 한방, 보건기관: 당월 요양 개시 일자 (월 통합 작성 명세서) 또는 내원 일자
- 약국: 조제 투약 일자';

COMMENT ON COLUMN nsc2_d20_1000.form_cd
    IS '서식코드

요양급여비용심사(의료보호) 청수거 및 명세서의 진료 구분명세서 서식 구분';

COMMENT ON COLUMN nsc2_d20_1000.mcare_subj_cd
    IS '진료과목코드

- (병원급 이상) 실제 진료를 받은 진료과목
- (의원) 상병명에 해당하는 진료과목

| 코드 | 진료과 |
| --- | --- |
| 00 | 일반의 |
| 01 | 내과 |
| 02 | 신경과 |
| 03 | 정신과 | 
| 04 | 외과 | 
| 05 | 정형외과 | 
| 06 | 신경외과 | 
| 07 | 흉부외과 | 
| 08 | 성형외과 |
| 09 | 마취통증의학과 | 
| 10 | 산부인과 |
| 11 | 소아청소년과 |
| 12 | 안과 | 
| 13 | 이비인후과 |
| 14 | 피부과 |
| 15 | 비뇨기과 |
| 16 | 영상의학과 |
| 17 | 방사선 종양학과 | 
| 18 | 병리과 |
| 19 | 진단검사의학과 | 
| 20 | 결핵과 |
| 21 | 재활의학과 |
| 22 | 핵의학과 |
| 23 | 가정의학과 |';

COMMENT ON COLUMN nsc2_d20_1000.sick_sym1
    IS '주상병

진료기간 중 치료나 검사 등에 대한 환자의 요구가 가장 컸던 상병';

COMMENT ON COLUMN nsc2_d20_1000.sick_sym2
    IS '부상병

진료기간 중 주상병과 함께 있었거나 발생된 상병으로 환자 진료에 영향을 주었던 상병';

COMMENT ON COLUMN nsc2_d20_1000.fst_hsptz_dt
    IS '최초 입원일자

입원 요양급여비용 분리청구의 경우 입원의 최초 입원개시일 기재';

COMMENT ON COLUMN nsc2_d20_1000.hsptz_path_type
    IS '입원 경로구분

· 병원급 이상 입원 환자의 경우 기재
· 1번째 자리(도착경로) 1: 타요양기관경유, 2: 응급구조대후송, 3: 기타
· 2번째 자리(입원경로) 1 응급실, 2 외래';

COMMENT ON COLUMN nsc2_d20_1000.ofij_type
    IS '공상 등 구분

· 공상(공무상 상해) 및 보훈, 군인, 차상위 희귀질환자 등에 해당되는
적용대상자 구분 코드
※ 각 제도별 대상자 선정기준 및 적용 내용은 관련 법령 및 고시 참고';

COMMENT ON COLUMN nsc2_d20_1000.oprtn_yn
    IS '수술여부 (0: 미수술, 1: 수술)';

COMMENT ON COLUMN nsc2_d20_1000.tot_prsc_dd_cnt
    IS '총 처방일수

· 처방전에 기재된 처방약품별 처방일수 중 가장 긴 처방일수
· 처방일자별로 처방내역이 다른 경우에는 처방일수를 합산하여 기재';

COMMENT ON COLUMN nsc2_d20_1000.mcare_rslt_type
    IS '진료결과구분

· 최종진료일의 환자상태 구분
· 1: 계속, 2: 이송, 3: 회송, 4 :사망, 9: 퇴원 또는 외래치료 종결';

COMMENT ON COLUMN nsc2_d20_1000.edc_add_rt
    IS '심결 가산율

· 요양기관의 종별 규모에 따라 시설, 인력, 장비 등의 투자비용
등을 고려하여 가산 적용되는 비율(%)
※ 요양개시일자 기준의 가산율, 공상가산율, 의료급여가산율 적용';

COMMENT ON COLUMN nsc2_d20_1000.ed_rc_tot_amt
    IS '심결요양 급여 비용 총액

· 심사결정 후 건강보험 총 요양 급여 비용';

COMMENT ON COLUMN nsc2_d20_1000.edc_insur_brdn_amt
    IS '심결 본인 부담금

· 심사 결정 후 수진자가 부담해야 할 금액';

COMMENT ON COLUMN nsc2_d20_1000.spcf_sym_type
    IS '특정기호구분

「본인일부부담금 산정특례에 관한 기준」 관련 특정기호 코드';




-- Table: public.nsc2_d30_1000

-- DROP TABLE public.nsc2_d30_1000;

CREATE TABLE public.nsc2_d30_1000
(
    rn_indi integer,
    rn_key bigint,
    mdcare_strt_dt integer,
    form_cd integer,
    mcare_desc_ln_no integer,
    cla_cd integer,
    item_cd integer,
    clsf_pttn_cd integer,
    mcare_div_cd character varying(10) COLLATE pg_catalog."default" DEFAULT NULL::character varying,
    uprc integer,
    dd1_mqty_freq integer,
    tot_mcnt integer,
    amt integer,
    time1_mdct_cpct integer,
    mdcn_ud integer,
    mcexp_type_cd integer,
    efmdc_clsf_no integer
)

TABLESPACE jnu_db;

ALTER TABLE public.nsc2_d30_1000
    OWNER to postgres;
COMMENT ON TABLE public.nsc2_d30_1000
    IS '치과 - 진료내역';

COMMENT ON COLUMN public.nsc2_d30_1000.rn_indi
    IS '개인고유번호(7자리), 연계코드';

COMMENT ON COLUMN public.nsc2_d30_1000.rn_key
    IS '청구고유번호 (14자리), 연계코드';

COMMENT ON COLUMN public.nsc2_d30_1000.mdcare_strt_dt
    IS '요양 게시일자

- 수진자가 진료를 받기 시작한 일자
- 의과, 치과, 한방, 보건기관: 당월 요양 개시 일자 (월 통합 작성 명세서) 또는 내원 일자
- 약국: 조제 투약 일자';

COMMENT ON COLUMN public.nsc2_d30_1000.form_cd
    IS '서식코드

요양급여비용심사(의료보호) 청수거 및 명세서의 진료 구분명세서 서식 구분';

COMMENT ON COLUMN public.nsc2_d30_1000.mcare_desc_ln_no
    IS '줄번호

처방전 발행건에 대한 세부내역 일련번호';

COMMENT ON COLUMN public.nsc2_d30_1000.cla_cd
    IS '항코드 (요양 급여 비용 명세서 항(1~10) 또는 C,M,T 코드), 코드표 참고
';

COMMENT ON COLUMN public.nsc2_d30_1000.item_cd
    IS '목코드 (18개항의 소분류 단위로 부여된 번호),  코드표 참고';

COMMENT ON COLUMN public.nsc2_d30_1000.clsf_pttn_cd
    IS '분류유형코드

명세서의 분류코드유형, 코드표 참고';

COMMENT ON COLUMN public.nsc2_d30_1000.mcare_div_cd
    IS '진료분류코드

진료분류코드(MCARE_DIV_CD) : 요양급여비용 분류항목별 코드
?   수가(행위)코드, 약품코드, 치료재료코드 등 진료내역 관련 고유코드

§ 해당 분류코드 확인은 수가파일, 약가파일 등 해당 파일을 다운받아 사용
ㄴ 건강보험심사평가원 홈페이지(hira.or.kr)

※  약제의 경우 처방전교부상세(60T)의 일반명 코드(GNL_NM_CD)와 동일한 값으로 구성
-  수가(행위)코드, 의약품 제품코드, 재료대코드 등이 포함되는데, 이 중 의약품 제품코드는 제품마다의 고유번호이기 때문에, 특정 약품에 대한 시장조사 등 사익 목적으로 이용될 가능성이 있어 기존 진료분류코드를 일반명코드 값으로 대체하였음
';

COMMENT ON COLUMN public.nsc2_d30_1000.uprc
    IS '단가 상세설명참고';

COMMENT ON COLUMN public.nsc2_d30_1000.dd1_mqty_freq
    IS '1일투여량또는실시횟수

1일 투여량을 기재, 의약품 및 처방내역 의약품의 경우 1일 투약횟수 기재
';

COMMENT ON COLUMN public.nsc2_d30_1000.tot_mcnt
    IS '총투여일수 또는 실시횟수
';

COMMENT ON COLUMN public.nsc2_d30_1000.amt
    IS '금액

단가 × (일일투여량 또는 실시횟수) × (총투여일수 또는 실시횟수)';

COMMENT ON COLUMN public.nsc2_d30_1000.time1_mdct_cpct
    IS '1회 투여 용량';

COMMENT ON COLUMN public.nsc2_d30_1000.mdcn_ud
    IS '약제상한차액

약제의 상한가와 요양기관이 구입한 단가와의 차액 중
100분의70에 해당하는 금액....※2010.11월 차수 추가
';

COMMENT ON COLUMN public.nsc2_d30_1000.mcexp_type_cd
    IS '진료비 구분 코드

1: 기본진료료
2: 진료행위료
3: 약품비
4: 치료재료료
5: 정액수가
';

COMMENT ON COLUMN public.nsc2_d30_1000.efmdc_clsf_no
    IS '약효 분류번호

· (표본코호트 2.0 DB 추가) ‘주성분코드’ 활용 권장';




-- Table: public.nsc2_d40_1000

-- DROP TABLE public.nsc2_d40_1000;

CREATE TABLE public.nsc2_d40_1000
(
    rn_indi integer,
    rn_key bigint,
    mdcare_strt_dt integer,
    form_cd character varying(10) COLLATE pg_catalog."default" DEFAULT NULL::character varying,
    mcex_sick_sym character varying(10) COLLATE pg_catalog."default" DEFAULT NULL::character varying,
    detail_tmsg_subj_cd character varying(10) COLLATE pg_catalog."default" DEFAULT NULL::character varying,
    sick_clsf_type character varying(10) COLLATE pg_catalog."default" DEFAULT NULL::character varying,
    std_yyyy integer
)

TABLESPACE jnu_db;

ALTER TABLE public.nsc2_d40_1000
    OWNER to postgres;
COMMENT ON TABLE public.nsc2_d40_1000
    IS '치과 - 상병내역';

COMMENT ON COLUMN public.nsc2_d40_1000.rn_indi
    IS '개인고유번호(7자리), 연계코드';

COMMENT ON COLUMN public.nsc2_d40_1000.rn_key
    IS '청구고유번호 (14자리), 연계코드';

COMMENT ON COLUMN public.nsc2_d40_1000.mdcare_strt_dt
    IS '요양 게시일자

- 수진자가 진료를 받기 시작한 일자
- 의과, 치과, 한방, 보건기관: 당월 요양 개시 일자 (월 통합 작성 명세서) 또는 내원 일자
- 약국: 조제 투약 일자';

COMMENT ON COLUMN public.nsc2_d40_1000.form_cd
    IS '서식코드

요양급여비용심사(의료보호) 청수거 및 명세서의 진료 구분명세서 서식 구분';

COMMENT ON COLUMN public.nsc2_d40_1000.mcex_sick_sym
    IS '요양급여상병기호

진료기간 중 환자가 진료 받은 해당상병';

COMMENT ON COLUMN public.nsc2_d40_1000.detail_tmsg_subj_cd
    IS '세부전문과목코드
';

COMMENT ON COLUMN public.nsc2_d40_1000.sick_clsf_type
    IS '상병분류구분코드

· 각 상병기호별 주상병, 부상병, 배제된 상병을 구분하는 구분자
1: 주상병
2: 부상병
3: 배제된 상병 (결측값도 존재)
(조건 설정:  1, 2, 결측 값 모두 선정)
';




-- Table: public.nsc2_d60_1000

-- DROP TABLE public.nsc2_d60_1000;

CREATE TABLE public.nsc2_d60_1000
(
    rn_indi integer,
    rn_key bigint,
    mdcare_strt_dt integer,
    form_cd character varying(10) COLLATE pg_catalog."default" DEFAULT NULL::character varying,
    mcare_desc_ln_no integer,
    clsf_pttn_cd integer,
    mprsc_time1_tuyak_cpct character varying(10) COLLATE pg_catalog."default" DEFAULT NULL::character varying,
    mprsc_dd1_tuyak_cpct integer,
    tot_mcnt integer,
    uprc character varying(10) COLLATE pg_catalog."default" DEFAULT NULL::character varying,
    amt integer,
    gnl_nm_cd character varying(10) COLLATE pg_catalog."default" DEFAULT NULL::character varying,
    efmdc_clsf_no integer
)

TABLESPACE jnu_db;

ALTER TABLE public.nsc2_d60_1000
    OWNER to postgres;
COMMENT ON TABLE public.nsc2_d60_1000
    IS '치과 - 처방전 교부 상세';

COMMENT ON COLUMN public.nsc2_d60_1000.rn_indi
    IS '개인고유번호(7자리), 연계코드';

COMMENT ON COLUMN public.nsc2_d60_1000.rn_key
    IS '청구고유번호 (14자리), 연계코드';

COMMENT ON COLUMN public.nsc2_d60_1000.mdcare_strt_dt
    IS '요양 게시일자

- 수진자가 진료를 받기 시작한 일자
- 의과, 치과, 한방, 보건기관: 당월 요양 개시 일자 (월 통합 작성 명세서) 또는 내원 일자
- 약국: 조제 투약 일자';

COMMENT ON COLUMN public.nsc2_d60_1000.form_cd
    IS '서식코드

요양급여비용심사(의료보호) 청수거 및 명세서의 진료 구분명세서 서식 구분';

COMMENT ON COLUMN public.nsc2_d60_1000.mcare_desc_ln_no
    IS '줄번호

처방전발행건 세부내역 일련번호
';

COMMENT ON COLUMN public.nsc2_d60_1000.clsf_pttn_cd
    IS '분류 유형코드

명세서의 분류코드유형, 코드표 참고';

COMMENT ON COLUMN public.nsc2_d60_1000.mprsc_time1_tuyak_cpct
    IS '1회투여량횟수

1회 투약량을 기재';

COMMENT ON COLUMN public.nsc2_d60_1000.mprsc_dd1_tuyak_cpct
    IS '1일 투약량

1회 투약량을 기재';

COMMENT ON COLUMN public.nsc2_d60_1000.tot_mcnt
    IS '총 투여 일수';

COMMENT ON COLUMN public.nsc2_d60_1000.uprc
    IS '단가';

COMMENT ON COLUMN public.nsc2_d60_1000.amt
    IS '금액 (1회 투여량 × 1일 투약량 × 총 투여일수 × 단가)';

COMMENT ON COLUMN public.nsc2_d60_1000.efmdc_clsf_no
    IS '약효 분류번호 (진료내역(T30) 설명 참고)';




-- Table: nsc2_k20_1000

-- DROP TABLE nsc2_k20_1000;

CREATE TABLE nsc2_k20_1000
(
    rn_indi integer,
    rn_key bigint,
    rn_inst integer,
    mdcare_strt_dt integer,
    form_cd character varying(10) COLLATE pg_catalog."default" DEFAULT NULL::character varying,
    mcare_subj_cd integer,
    sick_sym1 character varying(10) COLLATE pg_catalog."default" DEFAULT NULL::character varying,
    sick_sym2 character varying(10) COLLATE pg_catalog."default" DEFAULT NULL::character varying,
    fst_hsptz_dt character varying(10) COLLATE pg_catalog."default" DEFAULT NULL::character varying,
    hsptz_path_type character varying(10) COLLATE pg_catalog."default" DEFAULT NULL::character varying,
    ofij_type character varying(10) COLLATE pg_catalog."default" DEFAULT NULL::character varying,
    oprtn_yn integer,
    mdcare_dd_cnt integer,
    vshsp_dd_cnt integer,
    tot_prsc_dd_cnt integer,
    mcare_rslt_type integer,
    edc_add_rt double precision,
    ed_rc_tot_amt integer,
    edc_sba integer,
    edc_insur_brdn_amt integer,
    spcf_sym_type character varying(10) COLLATE pg_catalog."default" DEFAULT NULL::character varying,
    std_yyyy integer
)

TABLESPACE jnu_db;

ALTER TABLE nsc2_k20_1000
    OWNER to postgres;
COMMENT ON TABLE nsc2_k20_1000
    IS '치과 - 진료 명세서';

COMMENT ON COLUMN nsc2_k20_1000.rn_indi
    IS '개인고유번호(7자리), 연계코드';

COMMENT ON COLUMN nsc2_k20_1000.rn_key
    IS '청구고유번호 (14자리), 연계코드';

COMMENT ON COLUMN nsc2_k20_1000.rn_inst
    IS '요양기관고유번호(6자리), 연계코드';

COMMENT ON COLUMN nsc2_k20_1000.mdcare_strt_dt
    IS '요양 게시일자

- 수진자가 진료를 받기 시작한 일자
- 의과, 치과, 한방, 보건기관: 당월 요양 개시 일자 (월 통합 작성 명세서) 또는 내원 일자
- 약국: 조제 투약 일자';

COMMENT ON COLUMN nsc2_k20_1000.form_cd
    IS '서식코드

요양급여비용심사(의료보호) 청수거 및 명세서의 진료 구분명세서 서식 구분';

COMMENT ON COLUMN nsc2_k20_1000.mcare_subj_cd
    IS '진료과목코드

- (병원급 이상) 실제 진료를 받은 진료과목
- (의원) 상병명에 해당하는 진료과목

| 코드 | 진료과 |
| --- | --- |
| 00 | 일반의 |
| 01 | 내과 |
| 02 | 신경과 |
| 03 | 정신과 | 
| 04 | 외과 | 
| 05 | 정형외과 | 
| 06 | 신경외과 | 
| 07 | 흉부외과 | 
| 08 | 성형외과 |
| 09 | 마취통증의학과 | 
| 10 | 산부인과 |
| 11 | 소아청소년과 |
| 12 | 안과 | 
| 13 | 이비인후과 |
| 14 | 피부과 |
| 15 | 비뇨기과 |
| 16 | 영상의학과 |
| 17 | 방사선 종양학과 | 
| 18 | 병리과 |
| 19 | 진단검사의학과 | 
| 20 | 결핵과 |
| 21 | 재활의학과 |
| 22 | 핵의학과 |
| 23 | 가정의학과 |';

COMMENT ON COLUMN nsc2_k20_1000.sick_sym1
    IS '주상병

진료기간 중 치료나 검사 등에 대한 환자의 요구가 가장 컸던 상병';

COMMENT ON COLUMN nsc2_k20_1000.sick_sym2
    IS '부상병

진료기간 중 주상병과 함께 있었거나 발생된 상병으로 환자 진료에 영향을 주었던 상병';

COMMENT ON COLUMN nsc2_k20_1000.fst_hsptz_dt
    IS '최초 입원일자

입원 요양급여비용 분리청구의 경우 입원의 최초 입원개시일 기재';

COMMENT ON COLUMN nsc2_k20_1000.hsptz_path_type
    IS '입원 경로구분

· 병원급 이상 입원 환자의 경우 기재
· 1번째 자리(도착경로) 1: 타요양기관경유, 2: 응급구조대후송, 3: 기타
· 2번째 자리(입원경로) 1 응급실, 2 외래';

COMMENT ON COLUMN nsc2_k20_1000.ofij_type
    IS '공상 등 구분

· 공상(공무상 상해) 및 보훈, 군인, 차상위 희귀질환자 등에 해당되는
적용대상자 구분 코드
※ 각 제도별 대상자 선정기준 및 적용 내용은 관련 법령 및 고시 참고';

COMMENT ON COLUMN nsc2_k20_1000.oprtn_yn
    IS '수술여부 (0: 미수술, 1: 수술)';

COMMENT ON COLUMN nsc2_k20_1000.tot_prsc_dd_cnt
    IS '총 처방일수

· 처방전에 기재된 처방약품별 처방일수 중 가장 긴 처방일수
· 처방일자별로 처방내역이 다른 경우에는 처방일수를 합산하여 기재';

COMMENT ON COLUMN nsc2_k20_1000.mcare_rslt_type
    IS '진료결과구분

· 최종진료일의 환자상태 구분
· 1: 계속, 2: 이송, 3: 회송, 4 :사망, 9: 퇴원 또는 외래치료 종결';

COMMENT ON COLUMN nsc2_k20_1000.edc_add_rt
    IS '심결 가산율

· 요양기관의 종별 규모에 따라 시설, 인력, 장비 등의 투자비용
등을 고려하여 가산 적용되는 비율(%)
※ 요양개시일자 기준의 가산율, 공상가산율, 의료급여가산율 적용';

COMMENT ON COLUMN nsc2_k20_1000.ed_rc_tot_amt
    IS '심결요양 급여 비용 총액

· 심사결정 후 건강보험 총 요양 급여 비용';

COMMENT ON COLUMN nsc2_k20_1000.edc_insur_brdn_amt
    IS '심결 본인 부담금

· 심사 결정 후 수진자가 부담해야 할 금액';

COMMENT ON COLUMN nsc2_k20_1000.spcf_sym_type
    IS '특정기호구분

「본인일부부담금 산정특례에 관한 기준」 관련 특정기호 코드';




-- Table: public.nsc2_k30_1000

-- DROP TABLE public.nsc2_k30_1000;

CREATE TABLE public.nsc2_k30_1000
(
    rn_indi integer,
    rn_key bigint,
    mdcare_strt_dt integer,
    form_cd integer,
    mcare_desc_ln_no integer,
    cla_cd integer,
    item_cd integer,
    clsf_pttn_cd integer,
    mcare_div_cd character varying(10) COLLATE pg_catalog."default" DEFAULT NULL::character varying,
    uprc integer,
    dd1_mqty_freq integer,
    tot_mcnt integer,
    amt integer,
    time1_mdct_cpct integer,
    mdcn_ud integer,
    mcexp_type_cd integer,
    efmdc_clsf_no integer
)

TABLESPACE jnu_db;

ALTER TABLE public.nsc2_k30_1000
    OWNER to postgres;
COMMENT ON TABLE public.nsc2_k30_1000
    IS '치과 - 진료내역';

COMMENT ON COLUMN public.nsc2_k30_1000.rn_indi
    IS '개인고유번호(7자리), 연계코드';

COMMENT ON COLUMN public.nsc2_k30_1000.rn_key
    IS '청구고유번호 (14자리), 연계코드';

COMMENT ON COLUMN public.nsc2_k30_1000.mdcare_strt_dt
    IS '요양 게시일자

- 수진자가 진료를 받기 시작한 일자
- 의과, 치과, 한방, 보건기관: 당월 요양 개시 일자 (월 통합 작성 명세서) 또는 내원 일자
- 약국: 조제 투약 일자';

COMMENT ON COLUMN public.nsc2_k30_1000.form_cd
    IS '서식코드

요양급여비용심사(의료보호) 청수거 및 명세서의 진료 구분명세서 서식 구분';

COMMENT ON COLUMN public.nsc2_k30_1000.mcare_desc_ln_no
    IS '줄번호

처방전 발행건에 대한 세부내역 일련번호';

COMMENT ON COLUMN public.nsc2_k30_1000.cla_cd
    IS '항코드 (요양 급여 비용 명세서 항(1~10) 또는 C,M,T 코드), 코드표 참고
';

COMMENT ON COLUMN public.nsc2_k30_1000.item_cd
    IS '목코드 (18개항의 소분류 단위로 부여된 번호),  코드표 참고';

COMMENT ON COLUMN public.nsc2_k30_1000.clsf_pttn_cd
    IS '분류유형코드

명세서의 분류코드유형, 코드표 참고';

COMMENT ON COLUMN public.nsc2_k30_1000.mcare_div_cd
    IS '진료분류코드

진료분류코드(MCARE_DIV_CD) : 요양급여비용 분류항목별 코드
?   수가(행위)코드, 약품코드, 치료재료코드 등 진료내역 관련 고유코드

§ 해당 분류코드 확인은 수가파일, 약가파일 등 해당 파일을 다운받아 사용
ㄴ 건강보험심사평가원 홈페이지(hira.or.kr)

※  약제의 경우 처방전교부상세(60T)의 일반명 코드(GNL_NM_CD)와 동일한 값으로 구성
-  수가(행위)코드, 의약품 제품코드, 재료대코드 등이 포함되는데, 이 중 의약품 제품코드는 제품마다의 고유번호이기 때문에, 특정 약품에 대한 시장조사 등 사익 목적으로 이용될 가능성이 있어 기존 진료분류코드를 일반명코드 값으로 대체하였음
';

COMMENT ON COLUMN public.nsc2_k30_1000.uprc
    IS '단가 상세설명참고';

COMMENT ON COLUMN public.nsc2_k30_1000.dd1_mqty_freq
    IS '1일투여량또는실시횟수

1일 투여량을 기재, 의약품 및 처방내역 의약품의 경우 1일 투약횟수 기재
';

COMMENT ON COLUMN public.nsc2_k30_1000.tot_mcnt
    IS '총투여일수 또는 실시횟수
';

COMMENT ON COLUMN public.nsc2_k30_1000.amt
    IS '금액

단가 × (일일투여량 또는 실시횟수) × (총투여일수 또는 실시횟수)';

COMMENT ON COLUMN public.nsc2_k30_1000.time1_mdct_cpct
    IS '1회 투여 용량';

COMMENT ON COLUMN public.nsc2_k30_1000.mdcn_ud
    IS '약제상한차액

약제의 상한가와 요양기관이 구입한 단가와의 차액 중
100분의70에 해당하는 금액....※2010.11월 차수 추가
';

COMMENT ON COLUMN public.nsc2_k30_1000.mcexp_type_cd
    IS '진료비 구분 코드

1: 기본진료료
2: 진료행위료
3: 약품비
4: 치료재료료
5: 정액수가
';

COMMENT ON COLUMN public.nsc2_k30_1000.efmdc_clsf_no
    IS '약효 분류번호

· (표본코호트 2.0 DB 추가) ‘주성분코드’ 활용 권장';



-- Table: public.nsc2_k40_1000

-- DROP TABLE public.nsc2_k40_1000;

CREATE TABLE public.nsc2_k40_1000
(
    rn_indi integer,
    rn_key bigint,
    mdcare_strt_dt integer,
    form_cd character varying(10) COLLATE pg_catalog."default" DEFAULT NULL::character varying,
    mcex_sick_sym character varying(10) COLLATE pg_catalog."default" DEFAULT NULL::character varying,
    detail_tmsg_subj_cd character varying(10) COLLATE pg_catalog."default" DEFAULT NULL::character varying,
    sick_clsf_type character varying(10) COLLATE pg_catalog."default" DEFAULT NULL::character varying,
    std_yyyy integer
)

TABLESPACE jnu_db;

ALTER TABLE public.nsc2_k40_1000
    OWNER to postgres;
COMMENT ON TABLE public.nsc2_k40_1000
    IS '치과 - 상병내역';

COMMENT ON COLUMN public.nsc2_k40_1000.rn_indi
    IS '개인고유번호(7자리), 연계코드';

COMMENT ON COLUMN public.nsc2_k40_1000.rn_key
    IS '청구고유번호 (14자리), 연계코드';

COMMENT ON COLUMN public.nsc2_k40_1000.mdcare_strt_dt
    IS '요양 게시일자

- 수진자가 진료를 받기 시작한 일자
- 의과, 치과, 한방, 보건기관: 당월 요양 개시 일자 (월 통합 작성 명세서) 또는 내원 일자
- 약국: 조제 투약 일자';

COMMENT ON COLUMN public.nsc2_k40_1000.form_cd
    IS '서식코드

요양급여비용심사(의료보호) 청수거 및 명세서의 진료 구분명세서 서식 구분';

COMMENT ON COLUMN public.nsc2_k40_1000.mcex_sick_sym
    IS '요양급여상병기호

진료기간 중 환자가 진료 받은 해당상병';

COMMENT ON COLUMN public.nsc2_k40_1000.detail_tmsg_subj_cd
    IS '세부전문과목코드
';

COMMENT ON COLUMN public.nsc2_k40_1000.sick_clsf_type
    IS '상병분류구분코드

· 각 상병기호별 주상병, 부상병, 배제된 상병을 구분하는 구분자
1: 주상병
2: 부상병
3: 배제된 상병 (결측값도 존재)
(조건 설정:  1, 2, 결측 값 모두 선정)
';




CREATE TABLE nsc2_m20_1000
(
    rn_indi integer,
    rn_key bigint,
    rn_inst integer,
    mdcare_strt_dt integer,
    form_cd character varying(10) COLLATE pg_catalog."default" DEFAULT NULL::character varying,
    mcare_subj_cd integer,
    sick_sym1 character varying(10) COLLATE pg_catalog."default" DEFAULT NULL::character varying,
    sick_sym2 character varying(10) COLLATE pg_catalog."default" DEFAULT NULL::character varying,
    fst_hsptz_dt character varying(10) COLLATE pg_catalog."default" DEFAULT NULL::character varying,
    hsptz_path_type character varying(10) COLLATE pg_catalog."default" DEFAULT NULL::character varying,
    ofij_type character varying(10) COLLATE pg_catalog."default" DEFAULT NULL::character varying,
    oprtn_yn integer,
    mdcare_dd_cnt integer,
    vshsp_dd_cnt integer,
    tot_prsc_dd_cnt integer,
    mcare_rslt_type integer,
    edc_add_rt double precision,
    ed_rc_tot_amt integer,
    edc_sba integer,
    edc_insur_brdn_amt integer,
    spcf_sym_type character varying(10) COLLATE pg_catalog."default" DEFAULT NULL::character varying,
    std_yyyy integer
)

TABLESPACE jnu_db;

ALTER TABLE nsc2_m20_1000
    OWNER to postgres;
COMMENT ON TABLE nsc2_m20_1000
    IS '치과 - 진료 명세서';

COMMENT ON COLUMN nsc2_m20_1000.rn_indi
    IS '개인고유번호(7자리), 연계코드';

COMMENT ON COLUMN nsc2_m20_1000.rn_key
    IS '청구고유번호 (14자리), 연계코드';

COMMENT ON COLUMN nsc2_m20_1000.rn_inst
    IS '요양기관고유번호(6자리), 연계코드';

COMMENT ON COLUMN nsc2_m20_1000.mdcare_strt_dt
    IS '요양 게시일자

- 수진자가 진료를 받기 시작한 일자
- 의과, 치과, 한방, 보건기관: 당월 요양 개시 일자 (월 통합 작성 명세서) 또는 내원 일자
- 약국: 조제 투약 일자';

COMMENT ON COLUMN nsc2_m20_1000.form_cd
    IS '서식코드

요양급여비용심사(의료보호) 청수거 및 명세서의 진료 구분명세서 서식 구분';

COMMENT ON COLUMN nsc2_m20_1000.mcare_subj_cd
    IS '진료과목코드

- (병원급 이상) 실제 진료를 받은 진료과목
- (의원) 상병명에 해당하는 진료과목

| 코드 | 진료과 |
| --- | --- |
| 00 | 일반의 |
| 01 | 내과 |
| 02 | 신경과 |
| 03 | 정신과 | 
| 04 | 외과 | 
| 05 | 정형외과 | 
| 06 | 신경외과 | 
| 07 | 흉부외과 | 
| 08 | 성형외과 |
| 09 | 마취통증의학과 | 
| 10 | 산부인과 |
| 11 | 소아청소년과 |
| 12 | 안과 | 
| 13 | 이비인후과 |
| 14 | 피부과 |
| 15 | 비뇨기과 |
| 16 | 영상의학과 |
| 17 | 방사선 종양학과 | 
| 18 | 병리과 |
| 19 | 진단검사의학과 | 
| 20 | 결핵과 |
| 21 | 재활의학과 |
| 22 | 핵의학과 |
| 23 | 가정의학과 |';

COMMENT ON COLUMN nsc2_m20_1000.sick_sym1
    IS '주상병

진료기간 중 치료나 검사 등에 대한 환자의 요구가 가장 컸던 상병';

COMMENT ON COLUMN nsc2_m20_1000.sick_sym2
    IS '부상병

진료기간 중 주상병과 함께 있었거나 발생된 상병으로 환자 진료에 영향을 주었던 상병';

COMMENT ON COLUMN nsc2_m20_1000.fst_hsptz_dt
    IS '최초 입원일자

입원 요양급여비용 분리청구의 경우 입원의 최초 입원개시일 기재';

COMMENT ON COLUMN nsc2_m20_1000.hsptz_path_type
    IS '입원 경로구분

· 병원급 이상 입원 환자의 경우 기재
· 1번째 자리(도착경로) 1: 타요양기관경유, 2: 응급구조대후송, 3: 기타
· 2번째 자리(입원경로) 1 응급실, 2 외래';

COMMENT ON COLUMN nsc2_m20_1000.ofij_type
    IS '공상 등 구분

· 공상(공무상 상해) 및 보훈, 군인, 차상위 희귀질환자 등에 해당되는
적용대상자 구분 코드
※ 각 제도별 대상자 선정기준 및 적용 내용은 관련 법령 및 고시 참고';

COMMENT ON COLUMN nsc2_m20_1000.oprtn_yn
    IS '수술여부 (0: 미수술, 1: 수술)';

COMMENT ON COLUMN nsc2_m20_1000.tot_prsc_dd_cnt
    IS '총 처방일수

· 처방전에 기재된 처방약품별 처방일수 중 가장 긴 처방일수
· 처방일자별로 처방내역이 다른 경우에는 처방일수를 합산하여 기재';

COMMENT ON COLUMN nsc2_m20_1000.mcare_rslt_type
    IS '진료결과구분

· 최종진료일의 환자상태 구분
· 1: 계속, 2: 이송, 3: 회송, 4 :사망, 9: 퇴원 또는 외래치료 종결';

COMMENT ON COLUMN nsc2_m20_1000.edc_add_rt
    IS '심결 가산율

· 요양기관의 종별 규모에 따라 시설, 인력, 장비 등의 투자비용
등을 고려하여 가산 적용되는 비율(%)
※ 요양개시일자 기준의 가산율, 공상가산율, 의료급여가산율 적용';

COMMENT ON COLUMN nsc2_m20_1000.ed_rc_tot_amt
    IS '심결요양 급여 비용 총액

· 심사결정 후 건강보험 총 요양 급여 비용';

COMMENT ON COLUMN nsc2_m20_1000.edc_insur_brdn_amt
    IS '심결 본인 부담금

· 심사 결정 후 수진자가 부담해야 할 금액';

COMMENT ON COLUMN nsc2_m20_1000.spcf_sym_type
    IS '특정기호구분

「본인일부부담금 산정특례에 관한 기준」 관련 특정기호 코드';



-- Table: public.nsc2_m30_1000

-- DROP TABLE public.nsc2_m30_1000;

CREATE TABLE public.nsc2_m30_1000
(
    rn_indi integer,
    rn_key bigint,
    mdcare_strt_dt integer,
    form_cd integer,
    mcare_desc_ln_no integer,
    cla_cd integer,
    item_cd integer,
    clsf_pttn_cd integer,
    mcare_div_cd character varying(10) COLLATE pg_catalog."default" DEFAULT NULL::character varying,
    uprc integer,
    dd1_mqty_freq integer,
    tot_mcnt integer,
    amt integer,
    time1_mdct_cpct integer,
    mdcn_ud integer,
    mcexp_type_cd integer,
    efmdc_clsf_no integer
)

TABLESPACE jnu_db;

ALTER TABLE public.nsc2_m30_1000
    OWNER to postgres;
COMMENT ON TABLE public.nsc2_m30_1000
    IS '치과 - 진료내역';

COMMENT ON COLUMN public.nsc2_m30_1000.rn_indi
    IS '개인고유번호(7자리), 연계코드';

COMMENT ON COLUMN public.nsc2_m30_1000.rn_key
    IS '청구고유번호 (14자리), 연계코드';

COMMENT ON COLUMN public.nsc2_m30_1000.mdcare_strt_dt
    IS '요양 게시일자

- 수진자가 진료를 받기 시작한 일자
- 의과, 치과, 한방, 보건기관: 당월 요양 개시 일자 (월 통합 작성 명세서) 또는 내원 일자
- 약국: 조제 투약 일자';

COMMENT ON COLUMN public.nsc2_m30_1000.form_cd
    IS '서식코드

요양급여비용심사(의료보호) 청수거 및 명세서의 진료 구분명세서 서식 구분';

COMMENT ON COLUMN public.nsc2_m30_1000.mcare_desc_ln_no
    IS '줄번호

처방전 발행건에 대한 세부내역 일련번호';

COMMENT ON COLUMN public.nsc2_m30_1000.cla_cd
    IS '항코드 (요양 급여 비용 명세서 항(1~10) 또는 C,M,T 코드), 코드표 참고
';

COMMENT ON COLUMN public.nsc2_m30_1000.item_cd
    IS '목코드 (18개항의 소분류 단위로 부여된 번호),  코드표 참고';

COMMENT ON COLUMN public.nsc2_m30_1000.clsf_pttn_cd
    IS '분류유형코드

명세서의 분류코드유형, 코드표 참고';

COMMENT ON COLUMN public.nsc2_m30_1000.mcare_div_cd
    IS '진료분류코드

진료분류코드(MCARE_DIV_CD) : 요양급여비용 분류항목별 코드
?   수가(행위)코드, 약품코드, 치료재료코드 등 진료내역 관련 고유코드

§ 해당 분류코드 확인은 수가파일, 약가파일 등 해당 파일을 다운받아 사용
ㄴ 건강보험심사평가원 홈페이지(hira.or.kr)

※  약제의 경우 처방전교부상세(60T)의 일반명 코드(GNL_NM_CD)와 동일한 값으로 구성
-  수가(행위)코드, 의약품 제품코드, 재료대코드 등이 포함되는데, 이 중 의약품 제품코드는 제품마다의 고유번호이기 때문에, 특정 약품에 대한 시장조사 등 사익 목적으로 이용될 가능성이 있어 기존 진료분류코드를 일반명코드 값으로 대체하였음
';

COMMENT ON COLUMN public.nsc2_m30_1000.uprc
    IS '단가 상세설명참고';

COMMENT ON COLUMN public.nsc2_m30_1000.dd1_mqty_freq
    IS '1일투여량또는실시횟수

1일 투여량을 기재, 의약품 및 처방내역 의약품의 경우 1일 투약횟수 기재
';

COMMENT ON COLUMN public.nsc2_m30_1000.tot_mcnt
    IS '총투여일수 또는 실시횟수
';

COMMENT ON COLUMN public.nsc2_m30_1000.amt
    IS '금액

단가 × (일일투여량 또는 실시횟수) × (총투여일수 또는 실시횟수)';

COMMENT ON COLUMN public.nsc2_m30_1000.time1_mdct_cpct
    IS '1회 투여 용량';

COMMENT ON COLUMN public.nsc2_m30_1000.mdcn_ud
    IS '약제상한차액

약제의 상한가와 요양기관이 구입한 단가와의 차액 중
100분의70에 해당하는 금액....※2010.11월 차수 추가
';

COMMENT ON COLUMN public.nsc2_m30_1000.mcexp_type_cd
    IS '진료비 구분 코드

1: 기본진료료
2: 진료행위료
3: 약품비
4: 치료재료료
5: 정액수가
';

COMMENT ON COLUMN public.nsc2_m30_1000.efmdc_clsf_no
    IS '약효 분류번호

· (표본코호트 2.0 DB 추가) ‘주성분코드’ 활용 권장';



-- Table: public.nsc2_m40_1000

-- DROP TABLE public.nsc2_m40_1000;

CREATE TABLE public.nsc2_m40_1000
(
    rn_indi integer,
    rn_key bigint,
    mdcare_strt_dt integer,
    form_cd character varying(10) COLLATE pg_catalog."default" DEFAULT NULL::character varying,
    mcex_sick_sym character varying(10) COLLATE pg_catalog."default" DEFAULT NULL::character varying,
    detail_tmsg_subj_cd character varying(10) COLLATE pg_catalog."default" DEFAULT NULL::character varying,
    sick_clsf_type character varying(10) COLLATE pg_catalog."default" DEFAULT NULL::character varying,
    std_yyyy integer
)

TABLESPACE jnu_db;

ALTER TABLE public.nsc2_m40_1000
    OWNER to postgres;
COMMENT ON TABLE public.nsc2_m40_1000
    IS '치과 - 상병내역';

COMMENT ON COLUMN public.nsc2_m40_1000.rn_indi
    IS '개인고유번호(7자리), 연계코드';

COMMENT ON COLUMN public.nsc2_m40_1000.rn_key
    IS '청구고유번호 (14자리), 연계코드';

COMMENT ON COLUMN public.nsc2_m40_1000.mdcare_strt_dt
    IS '요양 게시일자

- 수진자가 진료를 받기 시작한 일자
- 의과, 치과, 한방, 보건기관: 당월 요양 개시 일자 (월 통합 작성 명세서) 또는 내원 일자
- 약국: 조제 투약 일자';

COMMENT ON COLUMN public.nsc2_m40_1000.form_cd
    IS '서식코드

요양급여비용심사(의료보호) 청수거 및 명세서의 진료 구분명세서 서식 구분';

COMMENT ON COLUMN public.nsc2_m40_1000.mcex_sick_sym
    IS '요양급여상병기호

진료기간 중 환자가 진료 받은 해당상병';

COMMENT ON COLUMN public.nsc2_m40_1000.detail_tmsg_subj_cd
    IS '세부전문과목코드
';

COMMENT ON COLUMN public.nsc2_m40_1000.sick_clsf_type
    IS '상병분류구분코드

· 각 상병기호별 주상병, 부상병, 배제된 상병을 구분하는 구분자
1: 주상병
2: 부상병
3: 배제된 상병 (결측값도 존재)
(조건 설정:  1, 2, 결측 값 모두 선정)
';



-- Table: public.nsc2_m60_1000

-- DROP TABLE public.nsc2_m60_1000;

CREATE TABLE public.nsc2_m60_1000
(
    rn_indi integer,
    rn_key bigint,
    mdcare_strt_dt integer,
    form_cd character varying(10) COLLATE pg_catalog."default" DEFAULT NULL::character varying,
    mcare_desc_ln_no integer,
    clsf_pttn_cd integer,
    mprsc_time1_tuyak_cpct character varying(10) COLLATE pg_catalog."default" DEFAULT NULL::character varying,
    mprsc_dd1_tuyak_cpct integer,
    tot_mcnt integer,
    uprc character varying(10) COLLATE pg_catalog."default" DEFAULT NULL::character varying,
    amt integer,
    gnl_nm_cd character varying(10) COLLATE pg_catalog."default" DEFAULT NULL::character varying,
    efmdc_clsf_no integer
)

TABLESPACE jnu_db;

ALTER TABLE public.nsc2_m60_1000
    OWNER to postgres;
COMMENT ON TABLE public.nsc2_m60_1000
    IS '치과 - 처방전 교부 상세';

COMMENT ON COLUMN public.nsc2_m60_1000.rn_indi
    IS '개인고유번호(7자리), 연계코드';

COMMENT ON COLUMN public.nsc2_m60_1000.rn_key
    IS '청구고유번호 (14자리), 연계코드';

COMMENT ON COLUMN public.nsc2_m60_1000.mdcare_strt_dt
    IS '요양 게시일자

- 수진자가 진료를 받기 시작한 일자
- 의과, 치과, 한방, 보건기관: 당월 요양 개시 일자 (월 통합 작성 명세서) 또는 내원 일자
- 약국: 조제 투약 일자';

COMMENT ON COLUMN public.nsc2_m60_1000.form_cd
    IS '서식코드

요양급여비용심사(의료보호) 청수거 및 명세서의 진료 구분명세서 서식 구분';

COMMENT ON COLUMN public.nsc2_m60_1000.mcare_desc_ln_no
    IS '줄번호

처방전발행건 세부내역 일련번호
';

COMMENT ON COLUMN public.nsc2_m60_1000.clsf_pttn_cd
    IS '분류 유형코드

명세서의 분류코드유형, 코드표 참고';

COMMENT ON COLUMN public.nsc2_m60_1000.mprsc_time1_tuyak_cpct
    IS '1회투여량횟수

1회 투약량을 기재';

COMMENT ON COLUMN public.nsc2_m60_1000.mprsc_dd1_tuyak_cpct
    IS '1일 투약량

1회 투약량을 기재';

COMMENT ON COLUMN public.nsc2_m60_1000.tot_mcnt
    IS '총 투여 일수';

COMMENT ON COLUMN public.nsc2_m60_1000.uprc
    IS '단가';

COMMENT ON COLUMN public.nsc2_m60_1000.amt
    IS '금액 (1회 투여량 × 1일 투약량 × 총 투여일수 × 단가)';

COMMENT ON COLUMN public.nsc2_m60_1000.efmdc_clsf_no
    IS '약효 분류번호 (진료내역(T30) 설명 참고)';
	
	
	
	
-- Table: nsc2_p20_1000

-- DROP TABLE nsc2_p20_1000;

CREATE TABLE nsc2_p20_1000
(
    rn_indi integer,
    rn_key bigint,
    rn_inst integer,
    mdcare_strt_dt integer,
    form_cd character varying(10) COLLATE pg_catalog."default" DEFAULT NULL::character varying,
    mcare_subj_cd integer,
    sick_sym1 character varying(10) COLLATE pg_catalog."default" DEFAULT NULL::character varying,
    sick_sym2 character varying(10) COLLATE pg_catalog."default" DEFAULT NULL::character varying,
    fst_hsptz_dt character varying(10) COLLATE pg_catalog."default" DEFAULT NULL::character varying,
    hsptz_path_type character varying(10) COLLATE pg_catalog."default" DEFAULT NULL::character varying,
    ofij_type character varying(10) COLLATE pg_catalog."default" DEFAULT NULL::character varying,
    oprtn_yn integer,
    mdcare_dd_cnt integer,
    vshsp_dd_cnt integer,
    tot_prsc_dd_cnt integer,
    mcare_rslt_type integer,
    edc_add_rt double precision,
    ed_rc_tot_amt integer,
    edc_sba integer,
    edc_insur_brdn_amt integer,
    spcf_sym_type character varying(10) COLLATE pg_catalog."default" DEFAULT NULL::character varying,
    std_yyyy integer
)

TABLESPACE jnu_db;

ALTER TABLE public.nsc2_p20_1000
    OWNER to postgres;
COMMENT ON TABLE nsc2_p20_1000
    IS '치과 - 진료 명세서';

COMMENT ON COLUMN nsc2_p20_1000.rn_indi
    IS '개인고유번호(7자리), 연계코드';

COMMENT ON COLUMN nsc2_p20_1000.rn_key
    IS '청구고유번호 (14자리), 연계코드';

COMMENT ON COLUMN nsc2_p20_1000.rn_inst
    IS '요양기관고유번호(6자리), 연계코드';

COMMENT ON COLUMN nsc2_p20_1000.mdcare_strt_dt
    IS '요양 게시일자

- 수진자가 진료를 받기 시작한 일자
- 의과, 치과, 한방, 보건기관: 당월 요양 개시 일자 (월 통합 작성 명세서) 또는 내원 일자
- 약국: 조제 투약 일자';

COMMENT ON COLUMN nsc2_p20_1000.form_cd
    IS '서식코드

요양급여비용심사(의료보호) 청수거 및 명세서의 진료 구분명세서 서식 구분';

COMMENT ON COLUMN nsc2_p20_1000.mcare_subj_cd
    IS '진료과목코드

- (병원급 이상) 실제 진료를 받은 진료과목
- (의원) 상병명에 해당하는 진료과목

| 코드 | 진료과 |
| --- | --- |
| 00 | 일반의 |
| 01 | 내과 |
| 02 | 신경과 |
| 03 | 정신과 | 
| 04 | 외과 | 
| 05 | 정형외과 | 
| 06 | 신경외과 | 
| 07 | 흉부외과 | 
| 08 | 성형외과 |
| 09 | 마취통증의학과 | 
| 10 | 산부인과 |
| 11 | 소아청소년과 |
| 12 | 안과 | 
| 13 | 이비인후과 |
| 14 | 피부과 |
| 15 | 비뇨기과 |
| 16 | 영상의학과 |
| 17 | 방사선 종양학과 | 
| 18 | 병리과 |
| 19 | 진단검사의학과 | 
| 20 | 결핵과 |
| 21 | 재활의학과 |
| 22 | 핵의학과 |
| 23 | 가정의학과 |';

COMMENT ON COLUMN nsc2_p20_1000.sick_sym1
    IS '주상병

진료기간 중 치료나 검사 등에 대한 환자의 요구가 가장 컸던 상병';

COMMENT ON COLUMN nsc2_p20_1000.sick_sym2
    IS '부상병

진료기간 중 주상병과 함께 있었거나 발생된 상병으로 환자 진료에 영향을 주었던 상병';

COMMENT ON COLUMN nsc2_p20_1000.fst_hsptz_dt
    IS '최초 입원일자

입원 요양급여비용 분리청구의 경우 입원의 최초 입원개시일 기재';

COMMENT ON COLUMN nsc2_p20_1000.hsptz_path_type
    IS '입원 경로구분

· 병원급 이상 입원 환자의 경우 기재
· 1번째 자리(도착경로) 1: 타요양기관경유, 2: 응급구조대후송, 3: 기타
· 2번째 자리(입원경로) 1 응급실, 2 외래';

COMMENT ON COLUMN nsc2_p20_1000.ofij_type
    IS '공상 등 구분

· 공상(공무상 상해) 및 보훈, 군인, 차상위 희귀질환자 등에 해당되는
적용대상자 구분 코드
※ 각 제도별 대상자 선정기준 및 적용 내용은 관련 법령 및 고시 참고';

COMMENT ON COLUMN nsc2_p20_1000.oprtn_yn
    IS '수술여부 (0: 미수술, 1: 수술)';

COMMENT ON COLUMN nsc2_p20_1000.tot_prsc_dd_cnt
    IS '총 처방일수

· 처방전에 기재된 처방약품별 처방일수 중 가장 긴 처방일수
· 처방일자별로 처방내역이 다른 경우에는 처방일수를 합산하여 기재';

COMMENT ON COLUMN nsc2_p20_1000.mcare_rslt_type
    IS '진료결과구분

· 최종진료일의 환자상태 구분
· 1: 계속, 2: 이송, 3: 회송, 4 :사망, 9: 퇴원 또는 외래치료 종결';

COMMENT ON COLUMN nsc2_p20_1000.edc_add_rt
    IS '심결 가산율

· 요양기관의 종별 규모에 따라 시설, 인력, 장비 등의 투자비용
등을 고려하여 가산 적용되는 비율(%)
※ 요양개시일자 기준의 가산율, 공상가산율, 의료급여가산율 적용';

COMMENT ON COLUMN nsc2_p20_1000.ed_rc_tot_amt
    IS '심결요양 급여 비용 총액

· 심사결정 후 건강보험 총 요양 급여 비용';

COMMENT ON COLUMN nsc2_p20_1000.edc_insur_brdn_amt
    IS '심결 본인 부담금

· 심사 결정 후 수진자가 부담해야 할 금액';

COMMENT ON COLUMN nsc2_p20_1000.spcf_sym_type
    IS '특정기호구분

「본인일부부담금 산정특례에 관한 기준」 관련 특정기호 코드';




-- Table: public.nsc2_p30_0208_1000

-- DROP TABLE public.nsc2_p30_0208_1000;

CREATE TABLE public.nsc2_p30_0208_1000
(
    rn_indi integer,
    rn_key bigint,
    mdcare_strt_dt integer,
    form_cd integer,
    mcare_desc_ln_no integer,
    cla_cd integer,
    item_cd integer,
    clsf_pttn_cd integer,
    mcare_div_cd character varying(10) COLLATE pg_catalog."default" DEFAULT NULL::character varying,
    uprc integer,
    dd1_mqty_freq integer,
    tot_mcnt integer,
    amt integer,
    time1_mdct_cpct integer,
    mdcn_ud integer,
    mcexp_type_cd integer,
    efmdc_clsf_no integer
)

TABLESPACE jnu_db;

ALTER TABLE public.nsc2_p30_0208_1000
    OWNER to postgres;
COMMENT ON TABLE public.nsc2_p30_0208_1000
    IS '치과 - 진료내역';

COMMENT ON COLUMN public.nsc2_p30_0208_1000.rn_indi
    IS '개인고유번호(7자리), 연계코드';

COMMENT ON COLUMN public.nsc2_p30_0208_1000.rn_key
    IS '청구고유번호 (14자리), 연계코드';

COMMENT ON COLUMN public.nsc2_p30_0208_1000.mdcare_strt_dt
    IS '요양 게시일자

- 수진자가 진료를 받기 시작한 일자
- 의과, 치과, 한방, 보건기관: 당월 요양 개시 일자 (월 통합 작성 명세서) 또는 내원 일자
- 약국: 조제 투약 일자';

COMMENT ON COLUMN public.nsc2_p30_0208_1000.form_cd
    IS '서식코드

요양급여비용심사(의료보호) 청수거 및 명세서의 진료 구분명세서 서식 구분';

COMMENT ON COLUMN public.nsc2_p30_0208_1000.mcare_desc_ln_no
    IS '줄번호

처방전 발행건에 대한 세부내역 일련번호';

COMMENT ON COLUMN public.nsc2_p30_0208_1000.cla_cd
    IS '항코드 (요양 급여 비용 명세서 항(1~10) 또는 C,M,T 코드), 코드표 참고
';

COMMENT ON COLUMN public.nsc2_p30_0208_1000.item_cd
    IS '목코드 (18개항의 소분류 단위로 부여된 번호),  코드표 참고';

COMMENT ON COLUMN public.nsc2_p30_0208_1000.clsf_pttn_cd
    IS '분류유형코드

명세서의 분류코드유형, 코드표 참고';

COMMENT ON COLUMN public.nsc2_p30_0208_1000.mcare_div_cd
    IS '진료분류코드

진료분류코드(MCARE_DIV_CD) : 요양급여비용 분류항목별 코드
?   수가(행위)코드, 약품코드, 치료재료코드 등 진료내역 관련 고유코드

§ 해당 분류코드 확인은 수가파일, 약가파일 등 해당 파일을 다운받아 사용
ㄴ 건강보험심사평가원 홈페이지(hira.or.kr)

※  약제의 경우 처방전교부상세(60T)의 일반명 코드(GNL_NM_CD)와 동일한 값으로 구성
-  수가(행위)코드, 의약품 제품코드, 재료대코드 등이 포함되는데, 이 중 의약품 제품코드는 제품마다의 고유번호이기 때문에, 특정 약품에 대한 시장조사 등 사익 목적으로 이용될 가능성이 있어 기존 진료분류코드를 일반명코드 값으로 대체하였음
';

COMMENT ON COLUMN public.nsc2_p30_0208_1000.uprc
    IS '단가 상세설명참고';

COMMENT ON COLUMN public.nsc2_p30_0208_1000.dd1_mqty_freq
    IS '1일투여량또는실시횟수

1일 투여량을 기재, 의약품 및 처방내역 의약품의 경우 1일 투약횟수 기재
';

COMMENT ON COLUMN public.nsc2_p30_0208_1000.tot_mcnt
    IS '총투여일수 또는 실시횟수
';

COMMENT ON COLUMN public.nsc2_p30_0208_1000.amt
    IS '금액

단가 × (일일투여량 또는 실시횟수) × (총투여일수 또는 실시횟수)';

COMMENT ON COLUMN public.nsc2_p30_0208_1000.time1_mdct_cpct
    IS '1회 투여 용량';

COMMENT ON COLUMN public.nsc2_p30_0208_1000.mdcn_ud
    IS '약제상한차액

약제의 상한가와 요양기관이 구입한 단가와의 차액 중
100분의70에 해당하는 금액....※2010.11월 차수 추가
';

COMMENT ON COLUMN public.nsc2_p30_0208_1000.mcexp_type_cd
    IS '진료비 구분 코드

1: 기본진료료
2: 진료행위료
3: 약품비
4: 치료재료료
5: 정액수가
';

COMMENT ON COLUMN public.nsc2_p30_0208_1000.efmdc_clsf_no
    IS '약효 분류번호

· (표본코호트 2.0 DB 추가) ‘주성분코드’ 활용 권장';




-- Table: public.nsc2_p30_0915_1000

-- DROP TABLE public.nsc2_p30_0915_1000;

CREATE TABLE public.nsc2_p30_0915_1000
(
    rn_indi integer,
    rn_key bigint,
    mdcare_strt_dt integer,
    form_cd integer,
    mcare_desc_ln_no integer,
    cla_cd integer,
    item_cd integer,
    clsf_pttn_cd integer,
    mcare_div_cd character varying(10) COLLATE pg_catalog."default" DEFAULT NULL::character varying,
    uprc integer,
    dd1_mqty_freq integer,
    tot_mcnt integer,
    amt integer,
    time1_mdct_cpct integer,
    mdcn_ud integer,
    mcexp_type_cd integer,
    efmdc_clsf_no integer
)

TABLESPACE jnu_db;

ALTER TABLE public.nsc2_p30_0915_1000
    OWNER to postgres;
COMMENT ON TABLE public.nsc2_p30_0915_1000
    IS '치과 - 진료내역';

COMMENT ON COLUMN public.nsc2_p30_0915_1000.rn_indi
    IS '개인고유번호(7자리), 연계코드';

COMMENT ON COLUMN public.nsc2_p30_0915_1000.rn_key
    IS '청구고유번호 (14자리), 연계코드';

COMMENT ON COLUMN public.nsc2_p30_0915_1000.mdcare_strt_dt
    IS '요양 게시일자

- 수진자가 진료를 받기 시작한 일자
- 의과, 치과, 한방, 보건기관: 당월 요양 개시 일자 (월 통합 작성 명세서) 또는 내원 일자
- 약국: 조제 투약 일자';

COMMENT ON COLUMN public.nsc2_p30_0915_1000.form_cd
    IS '서식코드

요양급여비용심사(의료보호) 청수거 및 명세서의 진료 구분명세서 서식 구분';

COMMENT ON COLUMN public.nsc2_p30_0915_1000.mcare_desc_ln_no
    IS '줄번호

처방전 발행건에 대한 세부내역 일련번호';

COMMENT ON COLUMN public.nsc2_p30_0915_1000.cla_cd
    IS '항코드 (요양 급여 비용 명세서 항(1~10) 또는 C,M,T 코드), 코드표 참고
';

COMMENT ON COLUMN public.nsc2_p30_0915_1000.item_cd
    IS '목코드 (18개항의 소분류 단위로 부여된 번호),  코드표 참고';

COMMENT ON COLUMN public.nsc2_p30_0915_1000.clsf_pttn_cd
    IS '분류유형코드

명세서의 분류코드유형, 코드표 참고';

COMMENT ON COLUMN public.nsc2_p30_0915_1000.mcare_div_cd
    IS '진료분류코드

진료분류코드(MCARE_DIV_CD) : 요양급여비용 분류항목별 코드
?   수가(행위)코드, 약품코드, 치료재료코드 등 진료내역 관련 고유코드

§ 해당 분류코드 확인은 수가파일, 약가파일 등 해당 파일을 다운받아 사용
ㄴ 건강보험심사평가원 홈페이지(hira.or.kr)

※  약제의 경우 처방전교부상세(60T)의 일반명 코드(GNL_NM_CD)와 동일한 값으로 구성
-  수가(행위)코드, 의약품 제품코드, 재료대코드 등이 포함되는데, 이 중 의약품 제품코드는 제품마다의 고유번호이기 때문에, 특정 약품에 대한 시장조사 등 사익 목적으로 이용될 가능성이 있어 기존 진료분류코드를 일반명코드 값으로 대체하였음
';

COMMENT ON COLUMN public.nsc2_p30_0915_1000.uprc
    IS '단가 상세설명참고';

COMMENT ON COLUMN public.nsc2_p30_0915_1000.dd1_mqty_freq
    IS '1일투여량또는실시횟수

1일 투여량을 기재, 의약품 및 처방내역 의약품의 경우 1일 투약횟수 기재
';

COMMENT ON COLUMN public.nsc2_p30_0915_1000.tot_mcnt
    IS '총투여일수 또는 실시횟수
';

COMMENT ON COLUMN public.nsc2_p30_0915_1000.amt
    IS '금액

단가 × (일일투여량 또는 실시횟수) × (총투여일수 또는 실시횟수)';

COMMENT ON COLUMN public.nsc2_p30_0915_1000.time1_mdct_cpct
    IS '1회 투여 용량';

COMMENT ON COLUMN public.nsc2_p30_0915_1000.mdcn_ud
    IS '약제상한차액

약제의 상한가와 요양기관이 구입한 단가와의 차액 중
100분의70에 해당하는 금액....※2010.11월 차수 추가
';

COMMENT ON COLUMN public.nsc2_p30_0915_1000.mcexp_type_cd
    IS '진료비 구분 코드

1: 기본진료료
2: 진료행위료
3: 약품비
4: 치료재료료
5: 정액수가
';

COMMENT ON COLUMN public.nsc2_p30_0915_1000.efmdc_clsf_no
    IS '약효 분류번호

· (표본코호트 2.0 DB 추가) ‘주성분코드’ 활용 권장';




-- Table: public.nsc2_g1e_0208_1000

-- DROP TABLE public.nsc2_g1e_0208_1000;

CREATE TABLE public.nsc2_g1e_0208_1000
(
    rn_indi integer,
    exmd_bz_yyyy integer,
    hme_yyyymm integer,
    q_phx1_dz_v0208 integer,
    q_phx1_yr text COLLATE pg_catalog."default",
    q_phx1_cr text COLLATE pg_catalog."default",
    q_phx2_dz_v0208 integer,
    q_phx2_yr text COLLATE pg_catalog."default",
    q_phx2_cr text COLLATE pg_catalog."default",
    q_phx3_dz_v0208 integer,
    q_phx3_yr text COLLATE pg_catalog."default",
    q_phx3_cr text COLLATE pg_catalog."default",
    q_fhx_lvdz text COLLATE pg_catalog."default",
    q_fhx_htn character varying(10) COLLATE pg_catalog."default" DEFAULT NULL::character varying,
    q_fhx_stk text COLLATE pg_catalog."default",
    q_fhx_htdz text COLLATE pg_catalog."default",
    q_fhx_dm integer,
    q_fhx_cc text COLLATE pg_catalog."default",
    q_awr_yn integer,
    q_awr_dz text COLLATE pg_catalog."default",
    q_ntr_prf integer,
    q_drk_frq_v0108 integer,
    q_drk_amt_v0108 text COLLATE pg_catalog."default",
    q_smk_yn integer,
    q_smk_now_amt_v0108 text COLLATE pg_catalog."default",
    q_smk_drt character varying(10) COLLATE pg_catalog."default" DEFAULT NULL::character varying,
    q_smk_strt_yr text COLLATE pg_catalog."default",
    q_smk_stop_yr text COLLATE pg_catalog."default",
    q_pa_frq integer,
    g1e_hght integer,
    g1e_wght integer,
    g1e_bmi double precision,
    g1e_wstc text COLLATE pg_catalog."default",
    g1e_va_lt double precision,
    g1e_va_rt double precision,
    g1e_ha_lt integer,
    g1e_ha_rt integer,
    g1e_bp_sys integer,
    g1e_bp_dia integer,
    g1e_urn_glu integer,
    g1e_urn_prot integer,
    g1e_urn_occ_bld integer,
    g1e_urn_ph integer,
    g1e_hgb double precision,
    g1e_fbs integer,
    g1e_tot_chol integer,
    g1e_sgot integer,
    g1e_sgpt integer,
    g1e_ggt integer,
    g1e_chst_xray_rst integer,
    g1e_ekg_rst character varying(10) COLLATE pg_catalog."default" DEFAULT NULL::character varying
)

TABLESPACE jnu_db;

ALTER TABLE public.nsc2_g1e_0208_1000
    OWNER to postgres;
COMMENT ON TABLE public.nsc2_g1e_0208_1000
    IS '건강 검진 테이블';

COMMENT ON COLUMN public.nsc2_g1e_0208_1000.rn_indi
    IS '개인고유번호(7자리), 연계코드';

COMMENT ON COLUMN public.nsc2_g1e_0208_1000.q_phx1_dz_v0208
    IS '병력코드1 (1~9)';

COMMENT ON COLUMN public.nsc2_g1e_0208_1000.q_phx1_yr
    IS '발생연도1';

COMMENT ON COLUMN public.nsc2_g1e_0208_1000.q_phx1_cr
    IS '완치유무1 (1,2)';

COMMENT ON COLUMN public.nsc2_g1e_0208_1000.q_phx2_dz_v0208
    IS '병력코드2 (1~9)';

COMMENT ON COLUMN public.nsc2_g1e_0208_1000.q_phx2_yr
    IS '발생연도2';

COMMENT ON COLUMN public.nsc2_g1e_0208_1000.q_phx2_cr
    IS '완치유무2 (1,2)';

COMMENT ON COLUMN public.nsc2_g1e_0208_1000.q_phx3_dz_v0208
    IS '병력코드3 (1~9)';

COMMENT ON COLUMN public.nsc2_g1e_0208_1000.q_phx3_yr
    IS '발생연도3';

COMMENT ON COLUMN public.nsc2_g1e_0208_1000.q_phx3_cr
    IS '완치유무3 (1,2)';

COMMENT ON COLUMN public.nsc2_g1e_0208_1000.q_fhx_lvdz
    IS '2002~2008년 문진
(가족력) 간장질환유무 (1,2)';

COMMENT ON COLUMN public.nsc2_g1e_0208_1000.q_fhx_htn
    IS '2002~2008년 문진
(가족력) 심근경색유무 (1,2)';

COMMENT ON COLUMN public.nsc2_g1e_0208_1000.q_fhx_stk
    IS '2002~2008년 문진
(가족력) 뇌졸증유무 (1,2)';

COMMENT ON COLUMN public.nsc2_g1e_0208_1000.q_fhx_htdz
    IS '2002~2008년 문진
(가족력) 심장병유무 (1,2)';

COMMENT ON COLUMN public.nsc2_g1e_0208_1000.q_fhx_dm
    IS '2002~2008년 문진
(가족력) 당뇨병유무 (1,2)';

COMMENT ON COLUMN public.nsc2_g1e_0208_1000.q_fhx_cc
    IS '2002~2008년 문진
(가족력) 암유무 (1,2)';

COMMENT ON COLUMN public.nsc2_g1e_0208_1000.q_awr_yn
    IS '(본인)과거 질환유무 (1,2)';

COMMENT ON COLUMN public.nsc2_g1e_0208_1000.q_awr_dz
    IS '(본인)과거 질환종류';

COMMENT ON COLUMN public.nsc2_g1e_0208_1000.q_smk_yn
    IS '2002~2008년 문진

흡연상태
1: 피우지 않는다
2: 과거에 피웠으나 지금은 끊었다
3: 현재도 피운다';

COMMENT ON COLUMN public.nsc2_g1e_0208_1000.q_smk_drt
    IS '2002~2008년 문진

(과거,현재)흡연기간
1: 5년 미만
2: 5~9년
3: 10~19년
4: 20~29년
5: 30년 이상';

COMMENT ON COLUMN public.nsc2_g1e_0208_1000.q_pa_frq
    IS '2002~2008년 문진

1주 운동횟수
1: 안한다
2: 1~2회
3: 3~4회
4: 5~6회
5: 거의 매일';

COMMENT ON COLUMN public.nsc2_g1e_0208_1000.g1e_hght
    IS '신장';

COMMENT ON COLUMN public.nsc2_g1e_0208_1000.g1e_wght
    IS '체중';

COMMENT ON COLUMN public.nsc2_g1e_0208_1000.g1e_va_lt
    IS '시력(좌)';

COMMENT ON COLUMN public.nsc2_g1e_0208_1000.g1e_va_rt
    IS '시력(우)';

COMMENT ON COLUMN public.nsc2_g1e_0208_1000.g1e_urn_ph
    IS '요PH - pH (0값 결측처리)';

COMMENT ON COLUMN public.nsc2_g1e_0208_1000.g1e_hgb
    IS '혈색소 - g/dL ( 0값 결측처리)';

COMMENT ON COLUMN public.nsc2_g1e_0208_1000.g1e_fbs
    IS '식전혈당(공복혈당)';

COMMENT ON COLUMN public.nsc2_g1e_0208_1000.g1e_tot_chol
    IS '총콜레스테롤';

COMMENT ON COLUMN public.nsc2_g1e_0208_1000.g1e_sgot
    IS '(혈청지오티)AST - U/L (0 값 결측처리)';

COMMENT ON COLUMN public.nsc2_g1e_0208_1000.g1e_sgpt
    IS '(혈청지피티)ALT - U/L (0 값 결측처리)';

COMMENT ON COLUMN public.nsc2_g1e_0208_1000.g1e_ggt
    IS '감마지티피 - U/L (0 값 결측처리)';
	
	
	
	
-- Table: public.nsc2_g1e_0915_1000

-- DROP TABLE public.nsc2_g1e_0915_1000;

CREATE TABLE public.nsc2_g1e_0915_1000
(
    rn_indi integer,
    exmd_bz_yyyy integer,
    hme_yyyymm integer,
    q_phx_tx_dm text COLLATE pg_catalog."default",
    q_fhx_htn text COLLATE pg_catalog."default",
    q_smk_yn integer,
    q_phx_dx_htn text COLLATE pg_catalog."default",
    q_phx_tx_etc text COLLATE pg_catalog."default",
    q_fhx_dm text COLLATE pg_catalog."default",
    g1e_bmi double precision,
    g1e_wstc integer,
    g1e_wght integer,
    g1e_hght integer,
    g1e_va_lt double precision,
    g1e_va_rt double precision,
    g1e_crtn double precision,
    g1e_hdl integer,
    g1e_fbs integer,
    g1e_tot_chol integer,
    g1e_sgot integer,
    g1e_sgpt integer,
    g1e_hgb double precision,
    g1e_tg integer,
    g1e_ggt integer,
    g1e_ldl integer,
    g1e_ha_lt integer,
    g1e_urn_prot integer,
    g1e_bp_sys integer,
    g1e_chst_xray_rst integer,
    g1e_bp_dia integer,
    g1e_ha_rt integer,
    g1e_gfr_mthd text COLLATE pg_catalog."default",
    g1e_gfr integer,
    q_pa_vd integer,
    q_pa_md integer,
    q_pa_walk integer,
    q_phx_tx_stk text COLLATE pg_catalog."default",
    q_phx_dx_etc text COLLATE pg_catalog."default",
    q_phx_tx_htdz text COLLATE pg_catalog."default",
    q_fhx_etc text COLLATE pg_catalog."default",
    q_fhx_stk text COLLATE pg_catalog."default",
    q_smk_now_drt text COLLATE pg_catalog."default",
    q_smk_pre_amt text COLLATE pg_catalog."default",
    q_phx_dx_ptb text COLLATE pg_catalog."default",
    q_phx_dx_dld text COLLATE pg_catalog."default",
    q_hbv_ag integer,
    q_phx_tx_ptb text COLLATE pg_catalog."default",
    q_phx_dx_dm text COLLATE pg_catalog."default",
    q_smk_now_amt_v09n text COLLATE pg_catalog."default",
    q_phx_tx_dld text COLLATE pg_catalog."default",
    q_fhx_htdz text COLLATE pg_catalog."default",
    q_drk_amt_v09n text COLLATE pg_catalog."default",
    q_drk_frq_v09n integer,
    q_phx_dx_stk text COLLATE pg_catalog."default",
    q_smk_pre_drt text COLLATE pg_catalog."default",
    q_phx_tx_htn text COLLATE pg_catalog."default",
    q_phx_dx_htdz text COLLATE pg_catalog."default"
)

TABLESPACE jnu_db;

ALTER TABLE public.nsc2_g1e_0915_1000
    OWNER to postgres;
COMMENT ON TABLE public.nsc2_g1e_0915_1000
    IS '건강 검진 테이블';

COMMENT ON COLUMN public.nsc2_g1e_0915_1000.rn_indi
    IS '개인고유번호(7자리), 연계코드';

COMMENT ON COLUMN public.nsc2_g1e_0915_1000.q_phx_tx_dm
    IS '? 2009~2015년 문진
(본인)당뇨병
약물치료여부 (0,1)';

COMMENT ON COLUMN public.nsc2_g1e_0915_1000.q_fhx_htn
    IS '(가족력)심근경색유무 (1,2)';

COMMENT ON COLUMN public.nsc2_g1e_0915_1000.q_smk_yn
    IS '2002~2008년 문진

흡연상태
1: 피우지 않는다
2: 과거에 피웠으나 지금은 끊었다
3: 현재도 피운다';

COMMENT ON COLUMN public.nsc2_g1e_0915_1000.q_phx_dx_htn
    IS '? 2009~2015년 문진
(본인)심근경색
과거병력유무 (0,1)';

COMMENT ON COLUMN public.nsc2_g1e_0915_1000.q_phx_tx_etc
    IS '? 2009~2015년 문진
(본인)기타 (암포함)질환
약물치료여부 (0, 1)
';

COMMENT ON COLUMN public.nsc2_g1e_0915_1000.q_fhx_dm
    IS '? 2002~2008년 문진
(가족력)당뇨병유무 (1,2)';

COMMENT ON COLUMN public.nsc2_g1e_0915_1000.g1e_wght
    IS '체중 kg (20미만 결측처리)';

COMMENT ON COLUMN public.nsc2_g1e_0915_1000.g1e_hght
    IS '신장 cm(100미만 결측처리)';

COMMENT ON COLUMN public.nsc2_g1e_0915_1000.g1e_va_lt
    IS '시력(좌) 0.1~2.0 9.9: 실명
';

COMMENT ON COLUMN public.nsc2_g1e_0915_1000.g1e_va_rt
    IS '시력(우) 0.1~2.0 9.9: 실명';

COMMENT ON COLUMN public.nsc2_g1e_0915_1000.g1e_crtn
    IS '혈청크레아티닌 mg/dL * 0값 결측처리';

COMMENT ON COLUMN public.nsc2_g1e_0915_1000.g1e_hdl
    IS 'HDL콜레스테롤 mg/dL * 0값 결측처리';

COMMENT ON COLUMN public.nsc2_g1e_0915_1000.g1e_fbs
    IS '식전혈당(공복혈당) - mg/dL * 0값 결측처리';

COMMENT ON COLUMN public.nsc2_g1e_0915_1000.g1e_tot_chol
    IS '총콜레스테롤 mg/dL * 0값 결측처리';

COMMENT ON COLUMN public.nsc2_g1e_0915_1000.g1e_sgot
    IS '(혈청지오티)AST U/L * 0값 결측처리';

COMMENT ON COLUMN public.nsc2_g1e_0915_1000.g1e_sgpt
    IS '(혈청지피티)ALT - U/L * 0값 결측처리';

COMMENT ON COLUMN public.nsc2_g1e_0915_1000.g1e_hgb
    IS '혈색소 g/dL * 0값 결측처리';

COMMENT ON COLUMN public.nsc2_g1e_0915_1000.g1e_tg
    IS '트리글리세라이드 mg/dL * 0값 결측처리';

COMMENT ON COLUMN public.nsc2_g1e_0915_1000.g1e_ggt
    IS '감마지티피 U/L * 0값 결측처리';

COMMENT ON COLUMN public.nsc2_g1e_0915_1000.g1e_ldl
    IS 'LDL콜레스테롤 mg/dL * 0값 결측처리';

COMMENT ON COLUMN public.nsc2_g1e_0915_1000.q_pa_vd
    IS '? 2009~2015년 문진
1주_20분이상 격렬한 운동
0 : 0일
1 : 1일
2 : 2일
3 : 3일
4 : 4일
5 : 5일
6 : 6일
7 : 7일';

COMMENT ON COLUMN public.nsc2_g1e_0915_1000.q_pa_walk
    IS '? 2009~2015년 문진
1주_총30분이상 걷기 운동
0 : 0일
1 : 1일
2 : 2일
3 : 3일
4 : 4일
5 : 5일
6 : 6일
7 : 7일';

COMMENT ON COLUMN public.nsc2_g1e_0915_1000.q_phx_tx_stk
    IS '? 2009~2015년 문진
(본인)뇌졸중
약물치료여부 (0,1)';

COMMENT ON COLUMN public.nsc2_g1e_0915_1000.q_phx_dx_etc
    IS '? 2009~2015년 문진
(본인)기타 (암포함)질환
과거병력유무 (0,1)';

COMMENT ON COLUMN public.nsc2_g1e_0915_1000.q_phx_tx_htdz
    IS '? 2009~2015년 문진
(본인)심장병
약물치료여부 (0,1)';

COMMENT ON COLUMN public.nsc2_g1e_0915_1000.q_fhx_etc
    IS '? 2009~2015년 문진
(가족력) 기타(암포함)환자유무 (0,1)';

COMMENT ON COLUMN public.nsc2_g1e_0915_1000.q_fhx_stk
    IS '? 2002~2008년 문진
(가족력) 뇌졸중유무 (1,2)';

COMMENT ON COLUMN public.nsc2_g1e_0915_1000.q_smk_now_drt
    IS '? 2009~2015년 문진
(현재) 흡연기간 _년';

COMMENT ON COLUMN public.nsc2_g1e_0915_1000.q_smk_pre_amt
    IS '? 2009~2015년 문진 _개피';

COMMENT ON COLUMN public.nsc2_g1e_0915_1000.q_phx_dx_ptb
    IS '? 2009~2015년 문진
(본인)폐결핵(2010~)
과거병력유무 (0,1)';

COMMENT ON COLUMN public.nsc2_g1e_0915_1000.q_phx_dx_dld
    IS '? 2009~2015년 문진
(본인)고지혈증 ( 이상지질혈증) (0,1)';

COMMENT ON COLUMN public.nsc2_g1e_0915_1000.q_hbv_ag
    IS '? 2009~2015년 문진
(본인)B형간염 항원보유자
1: 예 2: 아니오 3: 모름';

COMMENT ON COLUMN public.nsc2_g1e_0915_1000.q_phx_tx_ptb
    IS '(본인)폐결핵(2010~)
약물치료여부 (0,1)';

COMMENT ON COLUMN public.nsc2_g1e_0915_1000.q_phx_dx_dm
    IS '? 2009~2015년 문진
(본인)당뇨병 과거병력유무 (0,1)';

COMMENT ON COLUMN public.nsc2_g1e_0915_1000.q_phx_tx_dld
    IS '? 2009~2015년 문진
(본인)고지혈증 ( 이상지질혈증)
약물치료여부 (0,1)';

COMMENT ON COLUMN public.nsc2_g1e_0915_1000.q_fhx_htdz
    IS '? 2002~2008년 문진
(가족력) 심장병유무 (1,2)';

COMMENT ON COLUMN public.nsc2_g1e_0915_1000.q_phx_dx_stk
    IS '? 2009~2015년 문진
본인)뇌졸중
과거병력유무 (0,1)
';

COMMENT ON COLUMN public.nsc2_g1e_0915_1000.q_smk_pre_drt
    IS '? 2009~2015년 문진
(과거) 흡연기간 __년';

COMMENT ON COLUMN public.nsc2_g1e_0915_1000.q_phx_tx_htn
    IS '? 2009~2015년 문진
(본인)심근경색
약물치료여부 (0,1)';

COMMENT ON COLUMN public.nsc2_g1e_0915_1000.q_phx_dx_htdz
    IS '? 2009~2015년 문진
(본인)심장병
과거병력유무 (0,1)';




-- Table: public.nsc2_inst_1000

-- DROP TABLE public.nsc2_inst_1000;

CREATE TABLE public.nsc2_inst_1000
(
    std_yyyy integer,
    rn_inst integer,
    inst_clsfc_cd integer,
    sgg_inst integer,
    disp_subj_type integer,
    cnt_dr_tot integer,
    cnt_nrs_tot integer,
    cnt_bed_inp integer,
    cnt_bed_op integer,
    cnt_bed_er integer
)

TABLESPACE jnu_db;

ALTER TABLE public.nsc2_inst_1000
    OWNER to postgres;
COMMENT ON TABLE public.nsc2_inst_1000
    IS '요양 기관 테이블

상급종합병원

? 암, 중증질환 진료 등 전문적인 고난도 의료서비스를 제공하는 종
합병원(2012년부터 3년 주기로 지정)
?  보건복지부령으로 정하는 20개 이상의 진료과목을 갖추고 각 진료과목마다 전속하는 전문의를 둘 것
?  전문의가 되려는 자를 수련시키는 기관일 것
?  보건복지부령으로 정하는 인력·시설·장비 등을 갖출 것
?  질병군별 환자구성 비율이 보건복지부령으로 정하는 기준에 해당할 것

자료연결방법

? 요양기관테이블을 제외한 전체 테이블 : 개인일련번호(RN_INDI)
? 요양기관테이블 ↔진료테이블(20t) : 요양기관번호(RN_INST)
? 진료테이블의 세부DB(20t, 30t, 40t, 60t) : 청구일련번호(RN_KEY)
';

COMMENT ON COLUMN public.nsc2_inst_1000.std_yyyy
    IS '기준년도 (해당연도)';

COMMENT ON COLUMN public.nsc2_inst_1000.rn_inst
    IS '요양기관 고유번호(6자리), 연결변수';

COMMENT ON COLUMN public.nsc2_inst_1000.inst_clsfc_cd
    IS '요양기관 종별 코드';

COMMENT ON COLUMN public.nsc2_inst_1000.sgg_inst
    IS '요양기관 주소(시군구)정보';

COMMENT ON COLUMN public.nsc2_inst_1000.disp_subj_type
    IS '표시 과목구분';

COMMENT ON COLUMN public.nsc2_inst_1000.cnt_dr_tot
    IS '의사 수';

COMMENT ON COLUMN public.nsc2_inst_1000.cnt_nrs_tot
    IS '간호사 수';

COMMENT ON COLUMN public.nsc2_inst_1000.cnt_bed_inp
    IS '입원병상 수';

COMMENT ON COLUMN public.nsc2_inst_1000.cnt_bed_op
    IS '수술 병상 수';

COMMENT ON COLUMN public.nsc2_inst_1000.cnt_bed_er
    IS '응급 병상 수';
```


