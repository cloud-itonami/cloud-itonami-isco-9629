(ns elementarywork.advisor
  "Elementary Worker Advisor — proposing a site scheduling/logistics
  coordination operation (log a task/progress record, schedule a crew
  operation, flag a safety concern, coordinate a site-supplies
  procurement order) from a crew roster, site registration and safety-
  reporting policy. Swappable mock/llm; the advisor ONLY proposes —
  `elementarywork.governor` independently gates every proposal and
  always escalates safety concerns and above-threshold supply orders.
  The advisor never proposes to directly finalize a labour-work-
  execution decision (e.g. finalizing a specific labour-work operation),
  or a site-safety-clearance decision (e.g. declaring a site safety
  cleared), and never proposes to override a site safety supervisor's
  judgment — those stay permanently out of this actor's scope. Modeled
  closely on cloud-itonami-isco-9212's livestockfarm.advisor for the
  closest comparable generic elementary-occupation hazard pattern,
  adapted for ISCO-08 9629 'Elementary Workers Not Elsewhere
  Classified' — a residual category covering diverse manual/elementary
  labour work not captured by a more specific ISCO 9xxx code, so no
  single dominant hazard type applies (generic manual-lifting/strain
  hazard, varied-site-condition hazard and outdoor/indoor exposure all
  apply, in place of a single dominant hazard).

  A proposal: {:op :log-work-record|:schedule-crew-operation|
               :flag-safety-concern|:coordinate-supply-order
               :effect :propose :worker-id str :site-id str
               :cost number :hazard-type kw :task str :stake kw
               :confidence n :rationale str}")

(defprotocol Advisor
  (-advise [advisor store request] "request -> proposal map"))

(defn- rationale-for [op worker-id site-id hazard-type]
  (case op
    :log-work-record
    (str "logged work record for worker " worker-id " at site " site-id)

    :schedule-crew-operation
    (str "scheduled crew operation for elementary labour task at site " site-id)

    :flag-safety-concern
    (str "flagged " (name (or hazard-type :hazard)) " concern for worker "
         worker-id " at site " site-id " — routed for site safety supervisor review")

    :coordinate-supply-order
    (str "coordinated supply order for worker " worker-id " at site " site-id)

    (str "proposed " (name op) " for worker " worker-id " at site " site-id)))

(defn- infer [_store {:keys [op stake worker-id site-id cost hazard-type task]
                       :as request}]
  {:op op
   :effect :propose
   :worker-id worker-id
   :site-id site-id
   :cost cost
   :hazard-type hazard-type
   :task task
   :stake (or stake :low)
   :confidence (case (or stake :low) :high 0.7 :medium 0.85 :low 0.95)
   :rationale (rationale-for op worker-id site-id hazard-type)})

(defn mock-advisor []
  (reify Advisor
    (-advise [_ store request] (infer store request))))

(def ^:private system-prompt
  "You are an elementary-labour site scheduling/logistics coordination
   advisor. Given a request, propose an :op (one of :log-work-record,
   :schedule-crew-operation, :flag-safety-concern,
   :coordinate-supply-order), the :worker-id, :site-id, and any
   :cost/:hazard-type/:task fields, an honest :confidence and a
   :stake. Never propose an op outside this closed list, and never
   propose to directly finalize a labour-work-execution decision (e.g.
   finalizing a specific labour-work operation), or a site-safety-
   clearance decision (e.g. declaring a site safety cleared), or to
   override a site safety supervisor's judgment — those are always out
   of this actor's scope; it coordinates site scheduling/logistics only
   and never performs labour work itself or makes site-safety-clearance
   decisions itself. Safety concerns always require human sign-off
   regardless of confidence.")

(defn- parse-proposal [content]
  (try
    (let [p (read-string content)]
      (if (map? p)
        (assoc p :effect :propose)
        {:op :unknown :effect :propose :confidence 0.0 :stake :high
         :rationale "unparseable LLM response"}))
    (catch #?(:clj Exception :cljs js/Error) _
      {:op :unknown :effect :propose :confidence 0.0 :stake :high
       :rationale "LLM response parse failure"})))

(defn llm-advisor
  [chat-model model-generate-fn gen-opts]
  (reify Advisor
    (-advise [_ _store request]
      (let [msgs [{:role :system :content system-prompt}
                  {:role :user :content (str "operation request: " (pr-str request))}]
            resp (model-generate-fn chat-model msgs gen-opts)]
        (parse-proposal (:content resp))))))
