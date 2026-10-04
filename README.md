import streamlit as st
from matcher import match_score, skill_gap
from utils import read_pdf

st.set_page_config(page_title="ResumeMatch AI", page_icon="📄")
st.title("📄 ResumeMatch AI")
st.write("Upload your resume, paste a job description, and see how well they match.")

resume_file = st.file_uploader("Upload your resume (PDF)", type="pdf")
job_desc = st.text_area("Paste the job description here", height=200)

if st.button("Analyze"):
    if resume_file is None or not job_desc.strip():
        st.warning("Please upload a resume and paste a job description.")
    else:
        resume_text = read_pdf(resume_file)
        if not resume_text.strip():
            st.error("Could not read text from this PDF. Try a text-based (not scanned) PDF.")
        else:
            score = match_score(resume_text, job_desc)
            matched, missing = skill_gap(resume_text, job_desc)

            st.subheader(f"Match Score: {score}%")
            st.progress(min(int(score), 100))

            col1, col2 = st.columns(2)
            with col1:
                st.success("✅ Skills you have")
                st.write(", ".join(matched) if matched else "No listed skills matched.")
            with col2:
                st.error("❌ Skills to add")
                st.write(", ".join(missing) if missing else "Nothing missing. Great job!")

            if missing:
                st.info("Tip: if you genuinely know these skills, add them to your resume "
                        "with a project or experience that shows them.")
